---
title: Migrar aplicativos Tomcat para contêineres no Serviço de Kubernetes do Azure
description: Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Tomcat existente para ser executado em um contêiner do Serviço de Kubernetes do Azure.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.openlocfilehash: fafe7b16b14f43f6fe97090de8964c4e78796bda
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78893739"
---
# <a name="migrate-tomcat-applications-to-containers-on-azure-kubernetes-service"></a>Migrar aplicativos Tomcat para contêineres no Serviço de Kubernetes do Azure

Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Tomcat existente para ser executado em um contêiner do AKS (Serviço de Kubernetes do Azure).

## <a name="pre-migration"></a>Pré-migração

[!INCLUDE [inventory-external-resources](includes/migration/inventory-external-resources.md)]

[!INCLUDE [inventory-secrets](includes/migration/inventory-secrets.md)]

[!INCLUDE [inventory-persistence-usage](includes/migration/inventory-persistence-usage.md)]

<!-- AKS-specific addendum to inventory-persistence-usage -->
#### <a name="dynamic-or-internal-content"></a>Conteúdo dinâmico ou interno

Para arquivos que são frequentemente escritos e lidos pelo o aplicativo (como arquivos de dados temporários) ou arquivos estáticos que são visíveis somente para o aplicativo, você pode montar compartilhamentos do Armazenamento do Azure como volumes persistentes. Para obter mais informações, confira [Criar e usar dinamicamente um volume persistente com Arquivos do Azure no Serviço de Kubernetes do Azure](/azure/aks/azure-files-dynamic-pv).

### <a name="identify-session-persistence-mechanism"></a>Identificar o mecanismo de persistência da sessão

Para identificar o gerenciador de persistência de sessão em uso, inspecione os arquivos *context.xml* em seu aplicativo e a configuração do Tomcat. Procure o elemento `<Manager>` e observe o valor do atributo `className`.

As implementações internas de [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) do Tomcat, como [StandardManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html#Standard_Implementation) ou [FileStore](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html#Nested_Components), não são projetadas para uso com uma plataforma distribuída e dimensionada como o Kubernetes. O AKS pode balancear a carga entre vários pods e reiniciar de forma transparente qualquer Pod a qualquer momento, então a persistência de um estado mutável para um sistema de arquivos não é recomendada.

Se a persistência da sessão for necessária, você precisará usar uma implementação de `PersistentManager` alternativa que será gravada em um armazenamento de dados externo, como o Gerenciador de Sessão Dinâmica com o Cache Redis. Para obter mais informações, confira [Usar o Redis como um cache de sessão com o Tomcat](/azure/app-service/containers/configure-language-java#use-redis-as-a-session-cache-with-tomcat).

### <a name="special-cases"></a>Casos especiais

Determinados cenários de produção podem exigir alterações adicionais ou impor limitações adicionais. Embora esses cenários possam ser infrequentes, é importante garantir que eles não se apliquem ao seu aplicativo ou que sejam resolvidos corretamente.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Determinar se o aplicativo depende de trabalhos agendados

Trabalhos agendados, como tarefas do Agendador do Quartz ou trabalhos cron, não podem ser usados com implantações do Tomcat em contêineres. Se o aplicativo for escalado horizontalmente, um trabalho agendado poderá ser executado mais de uma vez por período agendado. Essa situação pode levar a consequências indesejadas.

Inventarie quaisquer trabalhos agendados, dentro ou fora do servidor de aplicativos.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Determinar se o aplicativo contém código específico do sistema operacional

Se seu aplicativo contiver qualquer código que esteja acomodando o sistema operacional no qual seu aplicativo estiver sendo executado, o aplicativo precisará ser refatorado para NÃO depender do sistema operacional subjacente. Por exemplo, qualquer uso de `/` ou `\` em caminhos de sistema de arquivos podem precisar ser substituídos por [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) ou [`Path.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-).

#### <a name="determine-whether-memoryrealm-is-used"></a>Determinar se MemoryRealm é usado

[MemoryRealm](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/realm/MemoryRealm.html) requer um arquivo XML persistente. No Kubernetes, esse arquivo precisará ser adicionado à imagem de contêiner ou carregado no [armazenamento compartilhado disponibilizado para os contêineres](#identify-session-persistence-mechanism). O parâmetro `pathName` precisará ser modificado de acordo.

Para determinar se `MemoryRealm` está sendo usado no momento, inspecione os arquivos *server.xml* e *context.xml* e pesquise por elementos `<Realm>` em que o atributo `className` está definido como `org.apache.catalina.realm.MemoryRealm`.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>Determinar se o acompanhamento de sessão SSL é usado

Em implantações em contêineres, as sessões SSL normalmente são descarregadas fora do contêiner do aplicativo, geralmente pelo controlador de entrada. Se o aplicativo exigir [acompanhamento de sessão SSL](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL), verifique se o tráfego SSL é passado diretamente para o contêiner do aplicativo.

#### <a name="determine-whether-accesslogvalve-is-used"></a>Determine se AccessLogValve é ou não usado

Se [AccessLogValve](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/valves/AccessLogValve.html) for usado, o parâmetro `directory` deverá ser definido para um [compartilhamento dos Arquivos do Azure montado](/azure/aks/azure-files-dynamic-pv) ou um dos respectivos subdiretórios.

### <a name="in-place-testing"></a>Teste in-loco

Antes de criar imagens de contêiner, migre seu aplicativo para o JDK e o Tomcat que você pretende usar no AKS. Teste seu aplicativo cuidadosamente para garantir a compatibilidade e o desempenho.

### <a name="parameterize-the-configuration"></a>Parametrizar a configuração

Na pré-migração, você provavelmente terá identificado segredos e dependências externas, como fontes de dados, nos arquivos *server.xml* e *context.xml*. Para cada item identificado assim, substitua qualquer nome de usuário, senha, cadeia de conexão ou URL por uma variável de ambiente.

Por exemplo, suponha que o arquivo *context.xml* contém o seguinte elemento:

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="jdbc:postgresql://postgresdb.contoso.com/wickedsecret?ssl=true"
    driverClassName="org.postgresql.Driver"
    username="postgres"
    password="t00secure2gue$$"
/>
```

Nesse caso, você poderia alterá-lo conforme mostrado no exemplo a seguir:

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="${postgresdb.connectionString}"
    driverClassName="org.postgresql.Driver"
    username="${postgresdb.username}"
    password="${postgresdb.password}"
/>
```

## <a name="migration"></a>Migração

Com exceção da primeira etapa ("Provisionar registro de contêiner e AKS"), recomendamos que você siga as etapas abaixo individualmente para cada aplicativo (arquivo WAR) que deseje migrar.

> [!NOTE]
> Algumas implantações do Tomcat podem ter vários aplicativos em execução em um único servidor Tomcat. Se esse for o caso em sua implantação, será altamente recomendável executar cada aplicativo em um pod separado. Isso permite que você otimize a utilização de recursos para cada aplicativo, minimizando a complexidade e o acoplamento.

### <a name="provision-container-registry-and-aks"></a>Provisionar registro de contêiner e AKS

Crie um registro de contêiner e um cluster do Kubernetes do Azure cuja entidade de serviço tenha a função de Leitor no registro. Verifique se você [escolheu o modelo de rede apropriado](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model) para os requisitos de rede do seu cluster.

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```

### <a name="prepare-the-deployment-artifacts"></a>Preparar os artefatos de implantação

Clone o [repositório GitHub do início rápido do Tomcat em contêineres](https://github.com/Azure/tomcat-container-quickstart). Ele contém um Dockerfile e arquivos de configuração do Tomcat com várias otimizações recomendadas. Nas etapas abaixo, descrevemos as modificações que você provavelmente precisará fazer nesses arquivos antes de criar a imagem de contêiner e implantá-la no AKS.

#### <a name="open-ports-for-clustering-if-needed"></a>Abrir portas para clustering, se necessário

Se você pretende usar o [clustering de Tomcat](https://tomcat.apache.org/tomcat-9.0-doc/cluster-howto.html) no AKS, verifique se os intervalos de portas necessários estão expostos no Dockerfile. Para especificar o endereço IP do servidor em *server.xml*, é necessário usar um valor de uma variável que é inicializada na inicialização do contêiner para o endereço IP do pod.

Como alternativa, o estado de sessão pode ser [persistido em um local alternativo](#identify-session-persistence-mechanism) para estar disponível entre as réplicas.

Para determinar se seu aplicativo usa clustering, procure o elemento `<Cluster>` dentro dos elementos `<Host>` ou `<Engine>` no arquivo *server.xml*.

#### <a name="add-jndi-resources"></a>Adicionar recursos de JNDI

Edite *server.xml* para adicionar os recursos que você preparou nas etapas de pré-migração, como fontes de dados.

Por exemplo:

```xml
<!-- Global JNDI resources
      Documentation at /docs/jndi-resources-howto.html
-->
<GlobalNamingResources>
    <!-- Editable user database that can also be used by
         UserDatabaseRealm to authenticate users
    -->
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml"
               />

    <!-- Migrated datasources here: -->
    <Resource
        name="jdbc/dbconnection"
        type="javax.sql.DataSource"
        url="${postgresdb.connectionString}"
        driverClassName="org.postgresql.Driver"
        username="${postgresdb.username}"
        password="${postgresdb.password}"
    />
    <!-- End of migrated datasources -->
</GlobalNamingResources>
```

### <a name="build-and-push-the-image"></a>Criar imagem e enviá-la por push

A maneira mais simples de criar a imagem e carregá-la no ACR (Registro de Contêiner do Azure) para uso pelo AKS é usar o comando `az acr build`. Este comando não requer que o Docker seja instalado no seu computador. Por exemplo, se você tiver o Dockerfile acima e o pacote de aplicativos *petclinic.war* no diretório atual, poderá criar a imagem de contêiner no ACR com uma etapa:

```bash
az acr build -t "${acrName}.azurecr.io/petclinic:{{.Run.ID}}" -r $acrName --build-arg APP_FILE=petclinic.war --build-arg=prod.server.xml .
```

Você poderá omitir o parâmetro `--build-arg APP_FILE...` se o seu arquivo WAR for nomeado *ROOT.war*. Você poderá omitir o parâmetro `--build-arg SERVER_XML...` se o seu arquivo XML do servidor for nomeado *server.xml*. Ambos os arquivos precisam estar no mesmo diretório que o *Dockerfile*.

Como alternativa, você pode usar a CLI do Docker para criar a imagem localmente. Essa abordagem pode simplificar o teste e refinar a imagem antes da implantação inicial no ACR. No entanto, ela requer que a CLI do Docker seja instalada e que o daemon do Docker esteja em execução.

```bash
# Build the image locally
sudo docker build . --build-arg APP_FILE=petclinic.war -t "${acrName}.azurecr.io/petclinic:1"

# Run the image locally
sudo docker run -d -p 8080:8080 "${acrName}.azurecr.io/petclinic:1"

# Your application can now be accessed with a browser at http://localhost:8080.

# Log into ACR
sudo az acr login -n $acrName

# Push the image to ACR
sudo docker push "${acrName}.azurecr.io/petclinic:1"
```

Para obter informações mais detalhadas sobre como criar e armazenar imagens de contêiner no Azure, consulte o respectivo [curso do Microsoft Learn](/learn/modules/build-and-store-container-images/).

### <a name="provision-a-public-ip-address"></a>Provisionar um endereço IP público

Se quiser que seu aplicativo esteja acessível de fora de suas redes internas ou virtuais, você precisará de um endereço IP estático público. Esse endereço IP deve ser provisionado dentro do grupo de recursos do nó do cluster.

```bash
nodeResourceGroup=$(az aks show -g $resourceGroup -n $aksName --query 'nodeResourceGroup' -o tsv)
publicIp=$(az network public-ip create -g $nodeResourceGroup -n applicationIp --sku Standard --allocation-method Static --query 'publicIp.ipAddress' -o tsv)
echo "Your public IP address is ${publicIp}."
```

### <a name="deploy-to-aks"></a>Implantar no AKS

[Crie e aplique seus arquivos YAML do Kubernetes](/azure/aks/kubernetes-walkthrough#run-the-application). Se você estiver criando um balanceador de carga externo (seja para seu aplicativo ou para um controlador de entrada), será necessário fornecer o endereço IP provisionado na seção anterior como o `LoadBalancerIP`.

Inclua [parâmetros externos como variáveis de ambiente](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/). Não inclua segredos (como senhas, chaves de API e cadeias de conexão JDBC). Os segredos são abordados na seção [Configurar o FlexVolume do KeyVault](#configure-keyvault-flexvolume).

### <a name="configure-persistent-storage"></a>Configurar um armazenamento persistente

Se seu aplicativo exigir armazenamento não volátil, configure um ou mais [Volumes Persistentes](/azure/aks/azure-disks-dynamic-pv).

Talvez você queira criar um volume persistente usando os Arquivos do Azure montados no diretório de logs do Tomcat ( */tomcat_logs*) para manter os logs centralmente. Para obter mais informações, confira [Criar e usar dinamicamente um volume persistente com Arquivos do Azure no AKS (Serviço de Kubernetes do Azure)](/azure/aks/azure-files-dynamic-pv).

### <a name="configure-keyvault-flexvolume"></a>Configurar FlexVolume do KeyVault

[Crie um Azure KeyVault](/azure/key-vault/quick-create-cli) e preencha todos os segredos necessários. Em seguida, configure um [FlexVolume do KeyVault](https://github.com/Azure/kubernetes-keyvault-flexvol/blob/master/README.md) para tornar esses segredos acessíveis para pods.

Você precisará modificar o script de inicialização (*startup.sh* no repositório GitHub [Tomcat em Contêineres](https://github.com/Azure/tomcat-container-quickstart)) para importar os certificados para o repositório de chaves local no contêiner.

### <a name="migrate-scheduled-jobs"></a>Migrar os trabalhos agendados

Para executar trabalhos agendados em seu cluster do AKS, defina [Trabalhos do Cron](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/) conforme necessário.

## <a name="post-migration"></a>Após a migração

Agora que você migrou seu aplicativo para o AKS, você deve verificar se ele funciona conforme o esperado. Depois de fazer isso, temos algumas recomendações para você que podem tornar seu aplicativo mais nativo da nuvem.

* Considere [adicionar um nome DNS](/azure/aks/ingress-static-ip#configure-a-dns-name) ao endereço IP alocado para o controlador de entrada ou o balanceador de carga do aplicativo.

* Considere [adicionar gráficos HELM ao seu aplicativo](https://helm.sh/docs/topics/charts/). Um gráfico do Helm permite que você parametrize a implantação do aplicativo para uso e personalização por um conjunto mais diversificado de clientes.

* Crie e implemente uma estratégia de DevOps. Para manter a confiabilidade, aumentando simultaneamente a velocidade de desenvolvimento, considere [automatizar implantações e testar com Azure Pipelines](/azure/devops/pipelines/ecosystems/kubernetes/aks-template).

* Habilite o [Monitoramento do Azure para o cluster](/azure/azure-monitor/insights/container-insights-enable-existing-clusters) para permitir a coleta de logs de contêiner, a utilização de acompanhamento e assim por diante.

* Considere a possibilidade de expor métricas específicas do aplicativo por meio do Prometheus. O Prometheus é uma estrutura de métricas open-source amplamente adotada na comunidade do Kubernetes. Você pode configurar o [recorte de métricas do Prometheus no Azure Monitor](/azure/azure-monitor/insights/container-insights-prometheus-integration), em vez de hospedar seu próprio servidor do Prometheus para habilitar a agregação de métricas de seus aplicativos e resposta automatizada para condições anormais ou escalonamento dessas condições.

* Criar a implementar uma estratégia de continuidade dos negócios e recuperação de desastre. Para aplicativos críticos, considere a possibilidade de [usar uma arquitetura de implantação em várias regiões](/azure/aks/operator-best-practices-multi-region).

* Examine a [política de suporte a versão do Kubernetes](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy). É sua responsabilidade continuar [atualizando seu cluster do AKS](/azure/aks/upgrade-cluster) para garantir que você esteja sempre executando uma versão compatível.

* Faça com que todos os membros da equipe responsáveis pela administração de clusters e pelo desenvolvimento de aplicativos [examinem as práticas recomendadas do AKS](/azure/aks/best-practices).

* Avalie os itens no arquivo *logging.properties*. Considere a possibilidade de eliminar ou reduzir parte da saída de log para melhorar o desempenho.

* Considere a possibilidade de [monitorar o tamanho do cache de código](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm) e adicione os parâmetros `-XX:InitialCodeCacheSize` e `-XX:ReservedCodeCacheSize` à variável `JAVA_OPTS` no Dockerfile para otimizar ainda mais o desempenho.
