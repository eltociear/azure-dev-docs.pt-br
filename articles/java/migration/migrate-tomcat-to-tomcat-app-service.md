---
title: Migrar aplicativos Tomcat para o Tomcat no Serviço de Aplicativo do Azure
description: Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Tomcat existente para ser executado no Serviço de Aplicativo do Azure usando o Tomcat.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.openlocfilehash: 6d2d18a6dbf87a97b806876a534a103dbbf88420
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988766"
---
# <a name="migrate-tomcat-applications-to-tomcat-on-azure-app-service"></a>Migrar aplicativos Tomcat para o Tomcat no Serviço de Aplicativo do Azure

Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Tomcat existente para ser executado no Serviço de Aplicativo do Azure usando o Tomcat 9.0.

## <a name="pre-migration"></a>Pré-migração

Antes de tudo, para garantir uma migração bem-sucedida, conclua as etapas de avaliação e de inventário descritas nas seções a seguir.

Se você não puder atender a nenhum desses requisitos de pré-migração, confira os seguintes guias de migração complementares:

* [Migrar aplicativos Tomcat para contêineres no Serviço de Kubernetes do Azure](migrate-tomcat-to-containers-on-azure-kubernetes-service.md)
* Migrar aplicativos do Tomcat para Máquinas Virtuais do Azure (diretrizes planejadas)

### <a name="switch-to-a-supported-platform"></a>Alternar para uma plataforma compatível

O Serviço de Aplicativo oferece versões específicas do Tomcat em versões específicas do Java. Para garantir a compatibilidade, migre o aplicativo para uma das versões com suporte do Tomcat e do Java no ambiente atual antes de continuar com qualquer uma das etapas restantes. É necessário testar completamente a configuração resultante. Use a versão estável mais recente da sua distribuição do Linux nesses testes.

[!INCLUDE [note-obtain-your-current-java-version-app-service](includes/note-obtain-your-current-java-version-app-service.md)]

Para determinar a sua versão atual do Tomcat, entre no servidor de produção e execute o seguinte comando:

```bash
${CATALINA_HOME}/bin/version.sh
```

Para obter a versão atual usada pelo Serviço de Aplicativo do Azure, baixe o [Tomcat 9](https://tomcat.apache.org/download-90.cgi), dependendo de qual versão você planeja usar no Serviço de Aplicativo do Azure.

[!INCLUDE [inventory-external-resources](includes/inventory-external-resources.md)]

[!INCLUDE [inventory-secrets](includes/inventory-secrets.md)]

### <a name="inventory-certificates"></a>Inventariar os certificados

[!INCLUDE [inventory-certificates](includes/inventory-certificates.md)]

[!INCLUDE [determine-whether-and-how-the-file-system-is-used](includes/determine-whether-and-how-the-file-system-is-used.md)]

<!-- App-Service-specific addendum to inventory-persistence-usage -->
#### <a name="dynamic-or-internal-content"></a>Conteúdo dinâmico ou interno

Para arquivos que são frequentemente escritos e lidos pelo o aplicativo (como arquivos de dados temporários) ou arquivos estáticos que são visíveis somente para o aplicativo, você pode montar o Armazenamento do Azure no sistema de arquivos do Serviço de Aplicativo. Para obter mais informações, confira [Servir conteúdo do Armazenamento do Microsoft Azure no Serviço de Aplicativo no Linux](/azure/app-service/containers/how-to-serve-content-from-azure-storage).

### <a name="identify-session-persistence-mechanism"></a>Identificar o mecanismo de persistência da sessão

Para identificar o gerenciador de persistência de sessão em uso, inspecione os arquivos *context.xml* em seu aplicativo e a configuração do Tomcat. Procure o elemento `<Manager>` e observe o valor do atributo `className`.

As implementações internas de [PersistentManager](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html) do Tomcat, como [StandardManager](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html#Standard_Implementation) ou [FileStore](https://tomcat.apache.org/tomcat-9.0-doc/config/manager.html#Nested_Components), não são projetadas para uso com uma plataforma distribuída e dimensionada como o Serviço de Aplicativo. Já que o Serviço de Aplicativo pode balancear a carga entre várias instâncias e reiniciar qualquer instância de maneira transparente a qualquer momento, então a persistência de um estado mutável para um sistema de arquivos não é recomendada.

Se a persistência da sessão for necessária, você precisará usar uma implementação de `PersistentManager` alternativa que será gravada em um armazenamento de dados externo, como o Gerenciador de Sessão Dinâmica com o Cache Redis. Para obter mais informações, confira [Usar o Redis como um cache de sessão com o Tomcat](/azure/app-service/containers/configure-language-java#use-redis-as-a-session-cache-with-tomcat).

### <a name="special-cases"></a>Casos especiais

Determinados cenários de produção podem exigir alterações adicionais ou impor limitações adicionais. Embora esses cenários sejam raros, é importante verificar se eles não se aplicam ao aplicativo ou estão resolvidos corretamente.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Determinar se o aplicativo depende de trabalhos agendados

Trabalhos agendados, como tarefas do Agendador do Quartz ou trabalhos cron, não podem ser usados com o Serviço de Aplicativo. O Serviço de Aplicativo não impedirá que você implante um aplicativo que contém tarefas agendadas internamente. No entanto, se o aplicativo for escalado horizontalmente, um mesmo trabalho agendado poderá ser executado mais de uma vez por período agendado. Essa situação pode levar a consequências indesejadas.

Inventarie quaisquer trabalhos agendados, dentro ou fora do servidor de aplicativos.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Determinar se o aplicativo contém código específico do sistema operacional

[!INCLUDE [determine-whether-your-application-contains-os-specific-code-no-title](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

#### <a name="determine-whether-tomcat-clustering-is-used"></a>Determinar se o clustering do Tomcat é ou não usado

O [clustering do Tomcat](https://tomcat.apache.org/tomcat-9.0-doc/cluster-howto.html) não é compatível com o Serviço de Aplicativo do Azure. Em vez disso, você pode configurar e gerenciar o dimensionamento e o balanceamento de carga por meio do Serviço de Aplicativo do Azure sem a funcionalidade específica do Tomcat. Você pode persistir o estado de sessão em um local alternativo para disponibilizá-lo entre réplicas. Para obter mais informações, confira [Identificar o mecanismo de persistência da sessão](#identify-session-persistence-mechanism).

Para determinar se seu aplicativo usa clustering, procure o elemento `<Cluster>` dentro dos elementos `<Host>` ou `<Engine>` no arquivo *server.xml*.

#### <a name="identify-all-outside-processesdaemons-running-on-the-production-servers"></a>Identificar todos os processos/daemons externos em execução nos servidores de produção

Você precisará migrar para outro lugar ou eliminar os processos em execução fora do servidor de aplicativos, como o monitoramento de daemons.

#### <a name="determine-whether-non-http-connectors-are-used"></a>Determinar se conectores não HTTP são ou não usados

O Serviço de Aplicativo dá suporte a apenas um único conector HTTP. Se seu aplicativo exigir conectores adicionais, como o conector AJP, não use o Serviço de Aplicativo.

Para identificar os conectores HTTP usados pelo seu aplicativo, procure elementos `<Connector>` dentro do arquivo *server.xml* em sua configuração do Tomcat.

#### <a name="determine-whether-memoryrealm-is-used"></a>Determinar se MemoryRealm é usado

[MemoryRealm](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/realm/MemoryRealm.html) requer um arquivo XML persistente. No Serviço de Aplicativo do Azure, você precisará carregar esse arquivo no diretório */home*, em um dos subdiretórios dele ou em um armazenamento montado. Em seguida, você precisará modificar o parâmetro `pathName` de acordo.

Para determinar se `MemoryRealm` está sendo usado no momento, inspecione os arquivos *server.xml* e *context.xml* e pesquise por elementos `<Realm>` em que o atributo `className` está definido como `org.apache.catalina.realm.MemoryRealm`.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>Determinar se o acompanhamento de sessão SSL é usado

O Serviço de Aplicativo realiza o descarregamento de sessão fora do runtime do Tomcat. Portanto, não é possível usar o [acompanhamento de sessão SSL](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL). Em vez disso, use um modo de acompanhamento de sessão diferente (`COOKIE` ou `URL`). Se você precisar de acompanhamento de sessão SSL, não use o Serviço de Aplicativo.

#### <a name="determine-whether-accesslogvalve-is-used"></a>Determine se AccessLogValve é ou não usado

Se você usar [AccessLogValve](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/valves/AccessLogValve.html), defina o parâmetro `directory` como `/home/LogFiles` ou um dos subdiretórios dele.

## <a name="migration"></a>Migração

### <a name="parameterize-the-configuration"></a>Parametrizar a configuração

Nas etapas de pré-migração, é provável que você tenha identificado alguns segredos e algumas dependências externas, como fontes de dados, nos arquivos *server.xml* e *context.xml*. Para cada item identificado, substitua qualquer nome de usuário, senha, cadeia de conexão ou URL por uma variável de ambiente.

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

### <a name="provision-an-app-service-plan"></a>Provisionar um plano do Serviço de Aplicativo

Na lista de planos de serviço disponíveis em [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/linux/), selecione o plano cujas especificações correspondem às do hardware de produção atual ou as superam.

> [!NOTE]
> Se você planeja executar implantações de preparo/canário ou usar slots de implantação, o plano do Serviço de Aplicativo precisará incluir essa capacidade adicional. É recomendável usar planos Premium ou superiores para aplicativos Java. Para obter mais informações, consulte [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](/azure/app-service/deploy-staging-slots).

Em seguida, crie o plano do Serviço de Aplicativo. Para obter mais informações, confira [Gerenciar um plano do Serviço de Aplicativo](/azure/app-service/app-service-plan-manage) no Azure.

### <a name="create-and-deploy-web-apps"></a>Criar e implantar aplicativos Web

Você precisará criar um aplicativo Web no plano do Serviço de Aplicativo (escolhendo uma versão do Tomcat como a pilha de runtime) para cada arquivo WAR implantado em seu servidor Tomcat.

> [!NOTE]
> Embora seja possível implantar vários arquivos WAR em um único aplicativo Web, isso é altamente indesejável. Implantar vários arquivos WAR em um único aplicativo Web impede que cada aplicativo seja dimensionado de acordo com suas próprias demandas de uso. Ele também adiciona complexidade a pipelines de implantação subsequentes. Se vários aplicativos precisarem estar disponíveis em uma única URL, considere a possibilidade de usar uma solução de roteamento, como o [Gateway de Aplicativo do Azure](/azure/application-gateway/).

#### <a name="maven-applications"></a>Aplicativos Maven

Se o seu aplicativo for criado com base em um arquivo POM do Maven, [use o plug-in do Aplicativo Web para Maven](/azure/app-service/containers/quickstart-java#configure-the-maven-plugin) para criar o aplicativo Web e implantar seu aplicativo.

#### <a name="non-maven-applications"></a>Aplicativos não Maven

Se não for possível usar o plug-in do Maven, você precisará provisionar o aplicativo Web por meio de outros mecanismos, como:

* [Azure portal](https://portal.azure.com/#create/Microsoft.WebSite)
* [CLI do Azure](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)
* [PowerShell do Azure](/powershell/module/az.websites/new-azwebapp)

Depois que o aplicativo Web tiver sido criado, use um dos [mecanismos de implantação disponíveis](/azure/app-service/deploy-zip) para implantar o aplicativo.

### <a name="migrate-jvm-runtime-options"></a>Migrar opções de runtime da JVM

Se o aplicativo exigir opções específicas de runtime, [use o mecanismo mais apropriado para especificá-las](/azure/app-service/containers/configure-language-java#set-java-runtime-options).

### <a name="populate-secrets"></a>Popular segredos

Use as Configurações do Aplicativo para armazenar os segredos específicos no aplicativo. Se você pretende usar os mesmos segredos entre vários aplicativos ou exigir funcionalidades de auditoria e políticas de acesso refinadas, [use o Azure Key Vault](/azure/app-service/containers/configure-language-java#use-keyvault-references) em vez das Configurações do Aplicativo.

[!INCLUDE [configure-custom-domain-and-ssl](includes/configure-custom-domain-and-ssl.md)]

[!INCLUDE [import-backend-certificates](includes/import-backend-certificates.md)]

### <a name="migrate-data-sources-libraries-and-jndi-resources"></a>Migrar fontes de dados, bibliotecas e recursos de JNDI

Para obter as etapas de configuração da fonte de dados, consulte a seção [Fontes de dados](/azure/app-service/containers/configure-language-java#data-sources) em [Configurar um aplicativo Java do Linux para o Serviço de Aplicativo do Azure](/azure/app-service/containers/configure-language-java).

[!INCLUDE[Tomcat datasource additional instructions](includes/tomcat-datasource-additional-instructions.md)]

Migre as dependências de classpath de nível de servidor adicionais seguindo [as mesmas etapas para arquivos JAR de fonte de dados](/azure/app-service/containers/configure-language-java#finalize-configuration).

Migre quaisquer [recursos JDNI de nível de servidor compartilhados](/azure/app-service/containers/configure-language-java#shared-server-level-resources) adicionais.

> [!NOTE]
> Se você estiver seguindo a arquitetura recomendada de um WAR por aplicativo Web, considere migrar bibliotecas de classpath de nível de servidor e recursos de JNDI para o aplicativo. Isso simplificará significativamente a governança de componentes e o gerenciamento de alterações.

### <a name="migrate-remaining-configuration"></a>Migrar a configuração restante

Ao concluir a seção anterior, você deve ter sua configuração de servidor personalizável em */Home/Tomcat/conf*.

Concluir a migração copiando qualquer configuração adicional (como [realms](https://tomcat.apache.org/tomcat-9.0-doc/config/realm.html) e [JASPIC](https://tomcat.apache.org/tomcat-9.0-doc/config/jaspic.html))

[!INCLUDE [migrate-scheduled-jobs](includes/migrate-scheduled-jobs.md)]

### <a name="restart-and-smoke-test"></a>Reinicialização e smoke test

Por fim, você precisará reiniciar seu aplicativo Web para aplicar todas as alterações de configuração. Após a conclusão da reinicialização, verifique se o aplicativo está sendo executado corretamente.

## <a name="post-migration"></a>Após a migração

Agora que você migrou seu aplicativo para o Serviço de Aplicativo do Azure, você deve verificar se ele funciona conforme o esperado. Depois de fazer isso, temos algumas recomendações para você que podem tornar seu aplicativo mais nativo da nuvem.

### <a name="recommendations"></a>Recomendações

* Se você optou por usar o diretório */home* para armazenamento de arquivos, considere [substituí-lo pelo Armazenamento do Azure](/azure/app-service/containers/how-to-serve-content-from-azure-storage).

* Se você tiver uma configuração no diretório */home* que contém cadeias de conexão, chaves SSL e outras informações de segredo, considere usar uma combinação do [Azure Key Vault](/azure/app-service/app-service-key-vault-references) e/ou uma [injeção de parâmetro com configurações de aplicativo](/azure/app-service/configure-common#configure-app-settings) sempre que possível.

* Considere [usar slots de implantação](/azure/app-service/deploy-staging-slots) para implantações confiáveis sem nenhum tempo de inatividade.

* Crie e implemente uma estratégia de DevOps. Para manter a confiabilidade, aumentando simultaneamente a velocidade de desenvolvimento, considere [automatizar implantações e testar com Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp). Ao usar slots de implantação, você pode [automatizar a implantação em um slot](/azure/devops/pipelines/targets/webapp?view=azure-devops&tabs=yaml#deploy-to-a-slot) e depois trocá-lo por outro.

* Criar a implementar uma estratégia de continuidade dos negócios e recuperação de desastre. Para aplicativos críticos, considere a possibilidade de [usar uma arquitetura de implantação em várias regiões](/azure/architecture/reference-architectures/app-service-web-app/multi-region).
