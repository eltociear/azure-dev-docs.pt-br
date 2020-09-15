---
title: Tutorial sobre como ler um segredo do Azure Key Vault em um aplicativo Spring Boot
description: Tutorial sobre como ler um segredo do Azure Key Vault em um aplicativo Spring Boot
services: key-vault
documentationcenter: java
ms.date: 08/15/2020
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: 17a154fe7f41e4079fd9c6dcf1358c2acd50367b
ms.sourcegitcommit: 035f485f182405fdf3e274ec4f70abfcf5fc9641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2020
ms.locfileid: "89511049"
---
# <a name="tutorial-reading-a-secret-from-azure-key-vault-in-a-spring-boot-application"></a>Tutorial: Ler um segredo do Azure Key Vault em um aplicativo Spring Boot

Os aplicativos Spring Boot externalizam informações confidenciais, como nomes de usuário e senhas.  Com a externalização de informações confidenciais, é possível ter uma melhor capacidade de manutenção e teste e mais segurança.  Armazenar segredos fora do código é melhor do que efetuar hard-coding das informações, ou embuti-las, no momento do build.

Este tutorial descreve como criar um aplicativo Spring Boot que lê um valor do Azure Key Vault e, em seguida, implantar o aplicativo no Serviço de Aplicativo do Azure e no Azure Spring Cloud.

> [!div class="checklist"]
> * Criar o Azure Key Vault e armazenar um segredo
> * Criar o aplicativo com o Spring Initializr
> * Adicionar a integração do Key Vault ao aplicativo
> * Implantar no Serviço de Aplicativo do Azure
> * Reimplantar no Serviço de Aplicativo do Azure com identidades gerenciadas para recursos do Azure
> * Implantar no Azure Spring Cloud

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura ativa do Azure.
  * Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).
* [Instale a CLI do Azure versão 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e a extensão do Azure Spring Cloud com o comando: `az extension add --name spring-cloud`
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) versão 3.0 ou posterior.
* O comando `curl`.  A maioria dos sistemas operacionais semelhantes ao UNIX tem esse comando pré-instalado.  Clientes específicos dos SOs estão disponíveis no [site oficial do curl](https://curl.haxx.se/).
* O comando `jq`. A maioria dos sistemas operacionais semelhantes ao UNIX tem esse comando pré-instalado.  Clientes específicos dos SOs estão disponíveis no [site oficial do jq](https://stedolan.github.io/jq/).

## <a name="create-a-new-azure-key-vault"></a>Criar um novo Azure Key Vault

As seções a seguir mostram como fazer logon no Azure e criar um Azure Key Vault.

### <a name="sign-into-azure-and-set-your-subscription"></a>Entre no Azure e configure sua assinatura

Primeiro, use as etapas a seguir para autenticar usando a CLI do Azure.

1. Também é possível fazer logoff e excluir alguns arquivos de autenticação para remover as credenciais remanescentes:

   ```azurecli
   az logout
   rm ~/.azure/accessTokens.json
   rm ~/.azure/azureProfile.json
   ```

1. Entre em sua conta do Azure usando a CLI do Azure:

   ```azurecli
   az login
   ```

   Siga as instruções na tela para concluir o processo de entrada.

1. Liste suas assinaturas:

   ```azurecli
   az account list
   ```

   O Azure retornará uma lista de suas assinaturas. Copie a `id` da assinatura que deseja usar, por exemplo:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "ssssssss-ssss-ssss-ssss-ssssssssssss",
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "tttttttt-tttt-tttt-tttt-tttttttttttt",
       "user": {
         "name": "contoso@microsoft.com",
         "type": "user"
       }
     }
   ]
   ```

1. Especifique o GUID para a assinatura que quer usar no Azure; por exemplo:

   ```azurecli
   az account set -s ssssssss-ssss-ssss-ssss-ssssssssssss
   ```

### <a name="create-a-service-principal-for-use-in-by-your-app"></a>Criar uma entidade de serviço para uso no aplicativo

As *entidades de serviço* do Azure AD fornecem acesso aos recursos do Azure em sua assinatura. Considere uma entidade de serviço como uma identidade de usuário para um serviço.  O "serviço" é qualquer aplicativo, serviço ou plataforma, incluindo o aplicativo de exemplo criado neste tutorial, que precisa acessar recursos do Azure. Você pode configurar uma entidade de serviço com direitos de acesso com escopo apenas nos recursos especificados. Em seguida, configure seu aplicativo ou serviço para usar as credenciais da entidade de serviço para acessar esses recursos.

Crie uma entidade de serviço com este comando.

```azurecli
az ad sp create-for-rbac --name contososp
```

O valor da opção `name` precisa ser exclusivo dentro de sua assinatura.  Tome nota dos valores retornados pelo comando, pois eles serão usados mais adiante no tutorial.  O JSON retornado será semelhante ao exemplo a seguir.

```json
{
  "appId": "8r7o486s-o5q9-450s-8457-pr26p86n0497",
  "displayName": "ejbcontososp",
  "name": "http://ejbcontososp",
  "password": "4bt.lCKJKlbYLn_3XF~wWtUwyHU0jKggu2",
  "tenant": "72s988os-86s1-41ns-91no-2d7cd011db47"
}
```

### <a name="create-the-key-vault-instance"></a>Criar a instância do Key Vault

O procedimento a seguir cria e inicializa o Key Vault.

1. Determine em qual região do Azure seus recursos serão mantidos.
   1. Você pode ver a [lista de regiões e suas localizações](https://azure.microsoft.com/regions/).
   1. Use o comando `az account list-locations` para localizar o `Name` correto da região escolhida.

      ```azurecli
      az account list-locations -o table
      ```

      Neste tutorial, usaremos `eastus`.
1. Crie um grupo de recursos para manter o Key Vault e o aplicativo do Serviço de Aplicativo.  O valor precisa ser exclusivo dentro da assinatura do Azure.  Neste tutorial, usaremos `contosorg`.

   ```azurecli
   az group create --name contosorg --location eastus
   ```

1. Crie um Key Vault no grupo de recursos.

   ```azurecli
   az keyvault create \
       --resource-group contosorg \
       --name contosokv \
       --enabled-for-deployment true \
       --enabled-for-disk-encryption true \
       --enabled-for-template-deployment true \
       --location eastus
       --query properties.vaultUri \
       --sku standard 
   ```

    > [!NOTE]
    > O valor da opção `--name` precisa ser exclusivo dentro da assinatura do Azure.

   Esta tabela explica as opções mostradas acima.

   | Parâmetro | Descrição |
   |---|---|
   | `enabled-for-deployment` | Especifica a [opção de implantação do Key Vault](/cli/azure/keyvault). |
   | `enabled-for-disk-encryption` | Especifica a [opção de criptografia do Key Vault](/cli/azure/keyvault). |
   | `enabled-for-template-deployment` | Especifica a [opção de criptografia do Key Vault](/cli/azure/keyvault). |
   | `location` | Especifica a [região do Azure](https://azure.microsoft.com/regions/) na qual seu grupo de recursos será hospedado. |
   | `name` | Especifica um nome exclusivo para o Key Vault. |
   | `query` | Recupere o URI do Key Vault na resposta.  Você precisa do URI para concluir o tutorial. |
   | `sku` | Especifica a [opção de SKU do Key Vault](/cli/azure/keyvault). |

   A CLI do Azure exibirá o URI do Key Vault, que será usado posteriormente. Por exemplo:

   ```output
   "https://contosokv.vault.azure.net/"
   ```

1. Configure o Key Vault para permitir as operações `get` e `list` dessa identidade gerenciada.  O valor da `object-id` é a `appId` do comando `az ad sp create-for-rbac` acima.

   ```azurecli
   az keyvault set-policy --name contosokv --spn http://ejbcontososp --secret-permissions get list
   ```

   A saída será um objeto JSON repleto de informações sobre o Key Vault.  Ele terá uma entrada `type` com o valor `Microsoft.KeyVault/vaults`.

   Esta tabela explica as propriedades mostradas acima.

   | Parâmetro | Descrição |
   |---|---|
   | name | O nome do Key Vault. |
   | spn | O `name` da saída do comando `az ad sp create-for-rbac` acima. |
   | secret-permissions | A lista de operações permitidas para a entidade de segurança nomeada. |

1. Armazene um segredo em seu novo Key Vault.  Um caso de uso comum é armazenar uma cadeia de conexão JDBC.  Por exemplo:

   ```azurecli
   az keyvault secret set --name "connectionString" \
       --vault-name "contosokv" \
       --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
   ```

   Esta tabela explica as opções mostradas acima.

   | Parâmetro | Descrição |
   |---|---|
   | `name` | Especifica o nome do seu segredo. |
   | `value` | Especifica o valor do seu segredo. |
   | `vault-name` | Especifica o nome do Key Vault informado anteriormente. |

   A CLI do Azure exibirá os resultados da criação do segredo. Por exemplo:

   ```json
   {
     "attributes": {
       "created": "2020-08-24T21:48:09+00:00",
       "enabled": true,
       "expires": null,
       "notBefore": null,
       "recoveryLevel": "Purgeable",
       "updated": "2020-08-24T21:48:09+00:00"
     },
     "contentType": null,
     "id": "https://contosokv.vault.azure.net/secrets/connectionString/123456789abcdef123456789abcdef",
     "kid": null,
     "managed": null,
     "tags": {
       "file-encoding": "utf-8"
     },
     "value": "jdbc:sqlserver://.database.windows.net:1433;database=DATABASE;"
   }
   ```

Agora que você criou um Key Vault e armazenou um segredo, a próxima seção mostrará como criar um aplicativo com o Spring Initializr.

## <a name="create-the-app-with-spring-initializr"></a>Criar o aplicativo com o Spring Initializr

Esta seção mostra como usar o Spring Initializr e `RestController` para criar e executar um aplicativo Spring Boot localmente.

1. Navegue até <https://start.spring.io/>.
1. Selecione as opções conforme ilustrado na imagem, seguindo esta lista.
   1. **Projeto**: `Maven Project`
   1. **Linguagem**: `Java`
   1. **Spring Boot**: `2.3.3`
   1. **Grupo**: `com.contoso`  (Você pode inserir qualquer nome de pacote Java válido aqui.)
   1. **Artefato**: *keyvault* (Você pode inserir qualquer nome de classe Java válido aqui.)
   1. **Empacotamento**: `Jar`
   1. **Java**: `11` (Você pode escolher o 8, mas este tutorial foi validado com o 11.)
1. Selecione **Adicionar Dependências...** .
1. No campo de texto, digite `Spring Web` e pressione Ctrl + Enter.
1. No campo de texto, digite `Azure Key Vault` e pressione Enter.  Sua tela deverá ser semelhante ao exemplo a seguir.
   :::image type="content" source="media/configure-spring-boot-starter-java-app-with-azure-key-vault/spring-initializr-choices.png" alt-text="Spring Initializr com as opções corretas selecionadas.":::
1. Na parte inferior da página, selecione **Gerar**.
1. Quando solicitado, baixe o projeto para um caminho no computador local.  Para este exemplo, usaremos um diretório *keyvault* no diretório inicial do usuário atual.  Os valores acima criarão um arquivo *keyvault.zip* nesse diretório.

Siga estas etapas para examinar o aplicativo e executá-lo localmente.

1. Descompacte o arquivo *keyvault.zip*.  O layout do arquivo será semelhante ao seguinte.  Neste tutorial, vamos ignorar o diretório *teste* e seu conteúdo.

   ```bash
   ├── HELP.md
   ├── mvnw
   ├── mvnw.cmd
   ├── pom.xml
   └── src
       ├── main
       │   ├── java
       │   │   └── com
       │   │       └── contoso
       │   │           └── keyvault
       │   │               └── KeyvaultApplication.java
       │   └── resources
       │       ├── application.properties
       │       ├── static
       │       └── templates
   ```

1. Abra o arquivo *KeyvaultApplication.java* no editor de texto.  Modifique o arquivo para que ele se assemelhe ao exemplo a seguir.

   * A classe está anotada com `@RestController`.  `@RestController` informa ao Spring Boot que a classe pode responder a solicitações HTTP RESTful.
   * A classe tem um método anotado com `@GetMapping(get)`.  `@GetMapping` instrui o Spring Boot a enviar solicitações HTTP com o caminho `/get` para esse método, permitindo que a resposta desse método seja retornada ao cliente HTTP.
   * A classe tem uma variável de instância privada `connectionString`.  O valor dessa variável de instância é retornado pelo método `get()`.

   ```java
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;

   @SpringBootApplication
   @RestController
   public class KeyvaultApplication {

      public static void main(String[] args) {
        SpringApplication.run(KeyvaultApplication.class, args);
      }

     @GetMapping("get")
     public String get() {
       return connectionString;
     }

     private String connectionString = "defaultValue\n";  

     public void run(String... varl) throws Exception {
       System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
     }  

   }
   ```

1. No diretório *keyvault* no nível superior, em que o arquivo *pom.xml* está localizado, insira `mvn spring-boot:run`.  
1. A mensagem **Inicialização concluída** na saída do comando significa que o servidor está pronto.  Em uma janela separada do shell, digite este comando.

   ```bash
   $ curl http://localhost:8080/get
   ```

   A saída mostrará `defaultValue`.

1. Encerre o processo sendo executado de `mvn spring-boot:run`.  Digite Ctrl-C ou use o comando `jps` para obter o PID do processo `Launcher` e encerrá-lo.

A próxima seção mostrará como adicionar a integração do Key Vault ao seu aplicativo em execução localmente.

## <a name="add-key-vault-integration-to-the-app"></a>Adicionar a integração do Key Vault ao aplicativo

As etapas a seguir mostram as modificações necessárias no aplicativo Spring Boot `KeyvaultApplication`.

Assim como o Key Vault permite externalizar segredos do código do aplicativo, a configuração do Spring permite externalizar a configuração do código.  A forma mais simples de configuração do Spring é o arquivo *application.properties*.  Em um projeto Maven, esse arquivo fica localizado em *src/main/resources/application.properties*.  Para auxiliar, o Spring Initializer inclui um arquivo de comprimento zero neste local.

Siga estas etapas para adicionar a configuração necessária a esse arquivo.

1. Abra *src/main/resources/application.properties* em um editor e faça com que ele tenha o conteúdo a seguir, ajustando os valores de acordo com sua assinatura do Azure.

   ```txt
   azure.keyvault.client-id=685on005-ns8q-4o04-8s16-n7os38o2ro5n
   azure.keyvault.client-key=4bt.lCKJKlbYLn_3XF~wWtUwyHU0jKggu2
   azure.keyvault.enabled=true
   azure.keyvault.tenant-id=72s988os-86s1-41ns-91no-2q7pq011qo47
   azure.keyvault.uri=https://contosokv.vault.azure.net/
   ```

   Esta tabela explica as propriedades mostradas acima.

   | Parâmetro | Descrição |
   |---|---|
   | azure.keyvault.client-id | O `appId` do JSON retornado de `az ad sp create-for-rbac`.|
   | azure.keyvault.client-key | O `password` do JSON retornado de `az ad sp create-for-rbac`.|
   | azure.keyvault.enabled | Essa configuração pode ser útil quando `enabled` ou `disabled` deve ser definido no momento da implantação.  Para obter mais informações sobre o Spring, confira a [Documentação do Spring Boot](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/#boot-features-external-config).
   | azure.keyvault.tenant-id | O `tenant` do JSON retornado de `az ad sp create-for-rbac`.|
   | azure.keyvault.uri | O valor de saída do comando `az keyvault create` acima. |

   A lista completa de propriedades disponíveis está documentada na [referência de propriedades](https://aka.ms/azure-spring-boot-starter-keyvault-secrets).

1. Salve o arquivo e feche-o.

Faça uma alteração simples no arquivo *KeyvaultApplication.java* (ou qualquer que seja o nome da classe no seu caso).

1. Abra *src/main/java/com/contoso/keyvault/KeyvaultApplication.java* em um editor de texto.
1. Adicione esta importação.

   ```java
   import org.springframework.beans.factory.annotation.Value;
   ```

1. Adicione uma anotação à variável da instância de `connectionString`.

   ```java
   @Value("${connectionString}")
   private String connectionString;  
   ```

   A integração do Key Vault fornece uma `PropertySource` do Spring que é populada com os valores do Key Vault.  Alguns detalhes adicionais da implementação estão disponíveis na [documentação de referência](https://aka.ms/azure-spring-boot-starter-keyvault-secrets).

1. No diretório *keyvault* no nível superior, em que o arquivo *pom.xml* está localizado, insira `mvn clean package spring-boot:run`.  
1. A mensagem **inicialização concluída** na saída do comando significa que o servidor está pronto.  Em uma janela separada do shell, digite este comando.

   ```bash
   $ curl http://localhost:8080/get
   ```

   A saída mostrará `jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE` em vez de `defaultValue`.

1. Encerre o processo sendo executado de `mvn spring-boot:run`.  Digite Ctrl-C ou use o comando `jps` para obter o PID do processo `Launcher` e encerrá-lo.


A próxima seção mostrará como implantar esse aplicativo no Serviço de Aplicativo do Azure.

## <a name="deploy-to-azure-app-service"></a>Implantar no Serviço de Aplicativo do Azure

Siga as etapas nesta seção para implantar o `KeyvaultApplication` no Serviço de Aplicativo do Azure.

### <a name="use-the-azure-maven-web-app-plugin-to-deploy-the-application-to-azure-app-service"></a>Usar o Plug-in de Aplicativo Web Maven do Azure para implantar o aplicativo no Serviço de Aplicativo do Azure

Siga estas etapas para preparar seu POM para implantar o `KeyvaultApplication` no Serviço de Aplicativo do Azure.

1. No diretório *keyvault* no nível superior, abra o arquivo *pom.xml*.
1. Na seção `<build><plugins>`, adicione o `azure-webapp-maven-plugin` inserindo esse XML.

   ```xml
    <plugin>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-webapp-maven-plugin</artifactId>
     <version>1.9.1</version>
    </plugin>
   ```

   > [!NOTE]
   > Não se preocupe com a formatação.  O `azure-webapp-maven-plugin` reformatará todo o POM durante esse processo.

1. Salve e feche o *pom.xml*.
1. Em uma linha de comando, invoque a meta de `config` do plug-in recém-adicionado.  O plug-in do Maven fará algumas perguntas e editará o arquivo *pom.xml* com base nas respostas.  Você fará edições adicionais no POM.

   ```bash
   mvn azure-webapp:config
   ```

1. Para o SO, certifique-se de que `linux` esteja selecionado.
1. Para o `javaVersion`, verifique se a versão do Java que você escolheu no Spring Initializr foi escolhida.  Escolhemos a `11` acima, então faremos a mesma escolha aqui.
1. Aceite os padrões para as perguntas restantes.
1. Quando solicitado a confirmar, responda S para continuar ou N para começar a responder as perguntas novamente.  Quando a execução do plug-in for concluída, você estará pronto para editar o POM.

Siga estas etapas para fazer mais edições necessárias no POM.

1. No diretório *keyvault* no nível superior, abra o arquivo *pom.xml*.
1. Localize a entrada `azure-webapp-maven-plugin` na seção `<plugins>.
1. Modifique o valor de `<resourceGroup>`, `<appName>` e `<region>`.  
   1. Defina o valor de `<resourceGroup>` como aquele que você especificou quando criou o Key Vault.
   1. Escolha um valor razoável para `<appName>` que seja exclusivo em sua assinatura.
   1. Defina o valor de `<region>` como aquele que você especificou quando criou o Key Vault.
1. Inclua um elemento `<appSettings>`, que faz com que o servidor escute na porta TCP 80.
1. O `<plugin>` completo modificado para `azure-webapp-maven-plugin` é mostrado a seguir.  Os valores que você precisa modificar estão indicados com `*`.

   ```xml
   <plugins> 
     <plugin> 
       <groupId>org.springframework.boot</groupId>  
       <artifactId>spring-boot-maven-plugin</artifactId> 
     </plugin>  
     <plugin> 
       <groupId>com.microsoft.azure</groupId>  
       <artifactId>azure-webapp-maven-plugin</artifactId>  
       <version>1.9.1</version>  
       <configuration>
         <schemaVersion>V2</schemaVersion>
         *<resourceGroup>contosorg</resourceGroup>
         *<appName>contosokeyvault</appName>
         <pricingTier>P1v2</pricingTier>
         *<region>eastus</region>
         <runtime>
           <os>linux</os>
           <javaVersion>java11</javaVersion>
           <webContainer>java11</webContainer>
         </runtime>
         *<!-- Begin of App Settings  -->
         *<appSettings>
         *  <property>
         *    <name>JAVA_OPTS</name>
         *    <value>-Dserver.port=80</value>
         *  </property>
         *</appSettings>
         *<!-- End of App Settings  -->          
         <deployment>
           <resources>
             <resource>
               <directory>${project.basedir}/target</directory>
               <includes>
                 <include>*.jar</include>
               </includes>
             </resource>
           </resources>
         </deployment>
       </configuration>
     </plugin> 
   </plugins>
   ```

1. Salve e feche o POM.
1. Implante o aplicativo no Serviço de Aplicativo do Azure.

   ```bash
   mvn -DskipTests clean package azure-webapp:deploy
   ```

1. O comando pode levar vários minutos, dependendo de vários fatores que estão além de seu controle.  Quando vir uma saída como esta, você saberá que o aplicativo foi implantado com êxito.

   ```bash
   [INFO] Deploying the zip package contosokeyvault-22b7c1a3-b41b-4082-a9f0-9339723fa36a11893059035499017844.zip...
   [INFO] Successfully deployed the artifact to https://contosokeyvault.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  01:45 min
   [INFO] Finished at: 2020-08-16T22:47:48-04:00
   [INFO] ------------------------------------------------------------------------
   ```

1. Aguarde de três a cinco minutos para permitir que a implantação seja concluída.  Em seguida, você pode acessar a implantação com um comando `curl` como o anterior, mas desta vez usando o nome do host mostrado na saída de `BUILD SUCCESS`.  Por exemplo, essa saída do comando `curl` indica êxito.

   ```bash
   curl https://contosokeyvault.azurewebsites.net/get
   jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;
   ```

Você implantou o aplicativo no Serviço de Aplicativo do Azure.

## <a name="redeploy-to-azure-app-service-and-use-managed-identities-for-azure-resources"></a>Reimplantar no Serviço de Aplicativo do Azure e usar identidades gerenciadas para recursos do Azure

Esta seção descreve como associar uma identidade ao recurso do Azure para o aplicativo.  É necessário fazer isso para que o Azure possa aplicar a segurança e monitorar o acesso.  Pagar apenas pelos recursos utilizados é um princípio fundamental da computação em nuvem.  Um controle tão refinado dos recursos só é possível quando todos os recursos estão associados a uma identidade.  O Serviço de Aplicativo do Azure e o Azure Key Vault são dois dos diversos serviços do Azure que aproveitam as identidades gerenciadas para recursos do Azure.  Saiba mais sobre essa importante tecnologia em [O que são identidades gerenciadas para recursos do Azure?](/azure/active-directory/managed-identities-azure-resources/overview)

> [!NOTE]
> Identidades gerenciadas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como MSI (Identidade de Serviço Gerenciada).

Siga estas etapas para criar a identidade gerenciada do aplicativo do Serviço de Aplicativo do Azure e, em seguida, permita que essa identidade acesse o Key Vault.

1. Crie uma identidade gerenciada para o aplicativo do Serviço de Aplicativo.  Substitua as opções pelos valores dos elementos `<resourceGroup>` e `<appName>` de seu POM.

   ```azurecli
   az webapp identity assign --resource-group contosorg --name contosokeyvault
   ```

   A saída será semelhante ao seguinte.  Anote o valor de `principalId` para a próxima etapa.

   ```json
   {
     "principalId": "2r7s6r00-92o9-4sq7-n10r-popq294ssr8s",
     "tenantId": "72s988os-86s1-41ns-91no-2q7pq011qo47",
     "type": "SystemAssigned",
     "userAssignedIdentities": null
   }
   ```

1. Edite *application.properties* para que ele nomeie a identidade gerenciada dos recursos do Azure criados na etapa anterior.

   1. Remova o `azure.keyvault.client-key`.
   1. Atualize o `azure.keyvault.client-id` para que tenha um valor do `principalId` da etapa anterior.  O arquivo concluído deve ter esta aparência:

   ```bash
   azure.keyvault.client-id=56rqs994-0o66-43o3-9roo-8e3534d0cb23
   azure.keyvault.enabled=true
   azure.keyvault.tenant-id=72s988os-86s1-41ns-91ab-2q7pq011qo47
   azure.keyvault.uri=https://contosokv.vault.azure.net/    
   ```

1. Configure o Key Vault para permitir as operações `get` e `list` dessa identidade gerenciada.  O valor da `object-id` é a `principalId` da saída anterior.

   ```azurecli
   az keyvault set-policy --name contosokv \
     --object-id 2r7s6r00-92o9-4sq7-n10r-popq294ssr8s --secret-permissions get list
   ```

   A saída será um objeto JSON repleto de informações sobre o Key Vault.  Ele terá uma entrada `type` com o valor `Microsoft.KeyVault/vaults`

   Esta tabela explica as propriedades mostradas acima.

   | Parâmetro | Descrição |
   |---|---|
   | name | O nome do Key Vault. | 
   | object-id | A `principalId` do comando anterior. |
   | secret-permissions | A lista de operações permitidas para a entidade de segurança nomeada. |

1. Empacote e reimplante o aplicativo.

   ```bash
   mvn -DskipTests clean package azure-webapp:deploy
   ```

1. Como uma boa prática, aguarde alguns minutos pela conclusão da implantação.  Em seguida, você pode contatar a implantação com um comando `curl` como o anterior, mas desta vez usando o nome do host mostrado na saída de `BUILD SUCCESS`.  Por exemplo, essa saída do comando `curl` indica êxito.

   ```bash
   curl https://contosokeyvault.azurewebsites.net/get
   jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;
   ```

Em vez de retornar `defaultValue`, o valor de `connectionString` é pesquisado e retornado do Key Vault.  Na próxima seção, implantaremos o mesmo aplicativo no Azure Spring Cloud.

## <a name="deploy-to-azure-spring-cloud"></a>Implantar no Azure Spring Cloud

O Azure Spring Cloud é uma plataforma totalmente gerenciada para implantar e executar aplicativos Spring Boot no Azure.  Para ter uma visão geral do Azure Spring Cloud, confira [O que é o Azure Spring Cloud?](/azure/spring-cloud/spring-cloud-overview)

Esta seção usará o aplicativo Spring Boot existente e o Key Vault criado anteriormente com uma nova instância do Azure Spring Cloud.

As etapas a seguir mostrarão como criar um recurso do Azure Spring Cloud e implantar o aplicativo para ele.  Verifique se você instalou a extensão da CLI do Azure para o Azure Spring Cloud, conforme mostrado nos [Pré-requisitos](#prerequisites).

1. Escolha um nome para a instância de serviço.  Para usar o Azure Spring Cloud em sua assinatura do Azure, você precisa criar um recurso do Azure do tipo Azure Spring Cloud.  Assim como ocorre com todos os outros recursos do Azure, a instância de serviço deve permanecer dentro de um grupo de recursos.  Use o grupo de recursos que você já criou para conter a instância do serviço.  Crie a instância de serviço com este comando. 

   ```bash
   az spring-cloud create --resource-group "contosorg" --name "contososvc" 
   ```

   Esse comando leva vários minutos para ser concluído.

1. Crie um aplicativo Spring Cloud dentro do serviço.

   ```bash
   az spring-cloud app create --resource-group "contosorg" --name "contosoascsapp" --assign-identity --is-public true
     --runtime-version Java_11 --service "contososvc"
   ```

   Esta tabela explica as opções mostradas acima.

   | Parâmetro | Descrição |
   |---|---|
   | assign-identity | Faz com que o serviço crie uma identidade para as identidades gerenciadas para recursos do Azure. |
   | is-public | Atribua um nome de domínio DNS público ao serviço. |
   | name | O nome do aplicativo. |
   | resource-group | O nome do grupo de recursos em que você criou a instância de serviço existente. |
   | runtime-version | Versão do runtime Java.  **O valor deve corresponder àquele escolhido no Spring Initializr acima.** |
   | serviço | O nome do serviço existente. |

   Para entender a diferença entre *serviço* e *aplicativo*, confira [Entender aplicativos e implantações no Azure Spring Cloud](/azure/spring-cloud/spring-cloud-concept-understand-app-and-deployment).

1. Obtenha a identidade gerenciada do recurso do Azure.  Use-a para configurar o Key Vault existente para permitir o acesso deste aplicativo.

   ```bash
   SERVICE_IDENTITY=$(az spring-cloud app show --resource-group "contosorg" --name "contosoascsapp" --service "contososvc" | jq -r '.identity.principalId')
   az keyvault set-policy --name "contosokv" --object-id <the value of the environment variable SERVICE_IDENTITY> --secret-permissions set get list
   ```

1. Como o aplicativo Spring Boot existente já tem um arquivo *application.properties* com a configuração necessária, podemos implantá-lo diretamente no Spring Cloud usando o comando a seguir.  Execute o comando no diretório que contém o POM.

   ```bash
   az spring-cloud app deploy --resource-group "contosorg" --name "contosoascsapp" --jar-path target/keyvault-0.0.1-SNAPSHOT.jar \
     --service "contososvc"
   ```

   Esse comando cria uma *Implantação* dentro do aplicativo, dentro do serviço.  Para obter mais detalhes sobre os conceitos de instâncias de serviço, aplicativos e implantações, confira [Entender aplicativos e implantações no Azure Spring Cloud](/azure/spring-cloud/spring-cloud-concept-understand-app-and-deployment).

   Se a implantação não for bem-sucedida, configure os logs para solução de problemas conforme descrito em [Configurar logs do aplicativo](https://aka.ms/azure-spring-cloud-configure-logs).  Os logs provavelmente terão informações úteis para diagnosticar e resolver o problema.

1. Quando o aplicativo tiver sido implantado com êxito, você poderá usar `curl` para verificar se a integração do Key Vault está funcionando.  Como você especificou `--is-public`, a URL padrão de seu serviço é `https://<service name>-<app name>.azuremicroservices.io/`.  Substituindo os valores adequados e acrescentando o valor da anotação `@GetMapping`, temos esse comando `curl`.

   ```bash
   curl https://contososvc-contosoascsapp.azuremicroservices.io/get
   ```

   A saída mostrará `jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE`.

## <a name="summary"></a>Resumo

Você criou um aplicativo Web Java usando o **Spring Initializr**.  Você criou um Azure Key Vault para armazenar informações confidenciais e configurou o aplicativo para recuperar informações do Key Vault.  Depois de testá-lo localmente, você implantou o aplicativo no Serviço de Aplicativo do Azure e no Azure Spring Cloud.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Configure um aplicativo Inicializador do Spring Boot para usar o Application Insights](configure-spring-boot-java-applicationinsights.md)
