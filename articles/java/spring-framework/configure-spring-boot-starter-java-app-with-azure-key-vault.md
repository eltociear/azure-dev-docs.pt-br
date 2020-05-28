---
title: Como usar o iniciador do Spring Boot para o Azure Key Vault
description: Descubra como configurar um aplicativo inicializador do Spring Boot com o iniciador do Azure Key Vault.
services: key-vault
documentationcenter: java
ms.date: 10/29/2019
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: a43b951fddfea01c4678fca3174f15a3b3d714f9
ms.sourcegitcommit: 9330d5af796b4b114466bbe75b8e18a9206f218e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83862809"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-key-vault"></a>Como usar o iniciador do Spring Boot para o Azure Key Vault

Este artigo demonstra como criar um aplicativo com o **[Spring Initializr]** , o qual usa o iniciador do Spring Boot para o Azure Key Vault para recuperar uma cadeia de conexão armazenada como um segredo em um cofre de chaves.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são obrigatórios para você concluir as etapas neste artigo:

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN] ou inscrever-se para uma [conta gratuita do Azure].
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) versão 3.0 ou posterior.

## <a name="create-an-app-using-spring-initializr"></a>Criar um aplicativo usando o Spring Initialzr

O procedimento a seguir cria o aplicativo usando o Spring Initializr.

1. Navegue até <https://start.spring.io/>.

1. Especifique que você deseja gerar um projeto do **Maven** com o **Java**.  

1. Insira o **Grupo** e os nomes do **Artefato** para seu aplicativo.

1. Na seção **Dependências**, insira o **Azure Key Vault**.

1. Role até a parte inferior da página e clique em **Gerar**.

   ![Gerar projeto do Spring Boot][secrets-01]

1. Quando solicitado, baixe o projeto para um caminho no computador local.

## <a name="sign-into-azure"></a>Entrar no Azure

O procedimento a seguir autentica o usuário na CLI do Azure.

1. Abra um prompt de comando.

1. Entre em sua conta do Azure usando a CLI do Azure:

   ```azurecli
   az login
   ```

Siga as instruções na tela para concluir o processo de entrada.

1. Liste suas assinaturas:

   ```azurecli
   az account list
   ```

   O Azure retornará uma lista de suas assinaturas, e será preciso copiar o GUID para a assinatura que deseja usar. Por exemplo:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "ssssssss-ssss-ssss-ssss-ssssssssssss",
       "isDefault": true,
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

1. Especifique o GUID para a conta que quer usar no Azure; por exemplo:

   ```azurecli
   az account set -s ssssssss-ssss-ssss-ssss-ssssssssssss
   ```

## <a name="create-a-new-azure-key-vault"></a>Crie um novo Azure Key Vault

O procedimento a seguir cria e inicializa o cofre de chaves.

1. Crie um grupo de recursos para os recursos do Azure que serão usados no seu cofre de chaves. Por exemplo:

   ```azurecli
   az group create --name vged-rg2 --location westus
   ```

   Em que:

   | Parâmetro | Descrição |
   |---|---|
   | `name` | Especifica um nome exclusivo para o grupo de recursos. |
   | `location` | Especifica a [região do Azure](https://azure.microsoft.com/regions/) na qual seu grupo de recursos será hospedado. |

   A CLI do Azure exibirá os resultados da criação do grupo de recursos, por exemplo:  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/resourceGroups/vged-rg2",
     "location": "westus",
     "managedBy": null,
     "name": "vged-rg2",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```

2. Crie um novo cofre de chaves no grupo de recursos. Por exemplo:

   ```azurecli
   az keyvault create --resource-group vged-rg2 \
       --name vgedkeyvault \
       --enabled-for-deployment true \
       --enabled-for-disk-encryption true \
       --enabled-for-template-deployment true \
       --sku standard --query properties.vaultUri \
       --location westus
   ```

   Em que:

   | Parâmetro | Descrição |
   |---|---|
   | `name` | Especifica um nome exclusivo para o seu cofre de chaves. |
   | `enabled-for-deployment` | Especifica a [opção de implantação do cofre de chaves](/cli/azure/keyvault). |
   | `enabled-for-disk-encryption` | Especifica a [opção de criptografia do cofre de chaves](/cli/azure/keyvault). |
   | `enabled-for-template-deployment` | Especifica a [opção de criptografia do cofre de chaves](/cli/azure/keyvault). |
   | `sku` | Especifica a [opção de SKU do cofre de chaves](/cli/azure/keyvault). |
   | `query` | Especifica um valor a ser recuperado da resposta, que é o URI do cofre de chaves que será necessário para concluir este tutorial. |
   | `location` | Especifica a [região do Azure](https://azure.microsoft.com/regions/) na qual seu grupo de recursos será hospedado. |

   A CLI do Azure exibirá o URI do cofre de chaves, que será usado posteriormente. Por exemplo:  

   ```output
   "https://vgedkeyvault.vault.azure.net"
   ```

3. Armazenar um segredo no seu novo cofre de chaves. Por exemplo:

   ```azurecli
   az keyvault secret set --vault-name "vgedkeyvault" \
       --name "connectionString" \
       --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
   ```

   Em que:

   | Parâmetro | Descrição |
   |---|---|
   | `vault-name` | Especifica o nome do cofre de chaves de antes. |
   | `name` | Especifica o nome do seu segredo. |
   | `value` | Especifica o valor do seu segredo. |

   A CLI do Azure exibirá os resultados da criação do segredo. Por exemplo:  

   ```json
   {
     "attributes": {
       "created": "2017-12-01T09:00:16+00:00",
       "enabled": true,
       "expires": null,
       "notBefore": null,
       "recoveryLevel": "Purgeable",
       "updated": "2017-12-01T09:00:16+00:00"
     },
     "contentType": null,
     "id": "https://vgedkeyvault.vault.azure.net/secrets/connectionString/123456789abcdef123456789abcdef",
     "kid": null,
     "managed": null,
     "tags": {
       "file-encoding": "utf-8"
     },
     "value": "jdbc:sqlserver://.database.windows.net:1433;database=DATABASE;"
   }
   ```

## <a name="configure-and-compile-your-app"></a>Configurar e compilar seu aplicativo

Use o procedimento a seguir para configurar e compilar seu aplicativo.

1. Extraia os arquivos do projeto Spring Boot que você baixou anteriormente em um diretório.

2. Navegue até a pasta *src/main/resources* no seu projeto e abra o arquivo *application.properties* em um editor de texto.

3. Adicione os valores do cofre de chaves usando os valores obtidos nas etapas concluídas anteriormente neste tutorial. Por exemplo:

   ```yaml
   azure.keyvault.enabled=true
   azure.keyvault.uri=Your-Keyvault-uri
   azure.keyvault.client-id=Your-Client-ID
   azure.keyvault.tenant-id=Your-Tenant-ID
   ```

   Em que:

   |          Parâmetro          |                                 Descrição                                 |
   |-----------------------------|-----------------------------------------------------------------------------|
   |    `azure.keyvault.uri`     |           Especifica o URI de quando você criou seu cofre de chaves.           |
    
    
4. Navegue até o arquivo de código-fonte principal de seu projeto, por exemplo: */src/main/java/com/vged/secrets*.

5. Abra o arquivo principal Java do aplicativo em um arquivo em um editor de texto; por exemplo: *SecretsApplication.java* e adicione as seguintes linhas ao arquivo:

   ```java
   package com.vged.secrets;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.boot.CommandLineRunner;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;
   
   @SpringBootApplication
   @RestController
   public class SecretsApplication implements CommandLineRunner {

      @Value("${connectionString}")
      private String connectionString;

      public static void main(String[] args) {
         SpringApplication.run(SecretsApplication.class, args);
      }
   
      @GetMapping("get")
      public String get() {
         return connectionString;
      }
   
      public void run(String... varl) throws Exception {
         System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
      }
   }
   ```
   Esse exemplo de código recupera a cadeia de conexão do cofre de chaves e a exibe na URL `https://{your-appservice-name}.azurewebsites.net/get`.

6. Salve e feche o arquivo Java.

7. Desabilite o teste e crie o arquivo JAR usando o Maven.
    
   ```bash
   mvn clean package -Dmaven.test.skip=true
   ```

## <a name="configure-maven-plugin-for-azure-app-service"></a>Configurar o plug-in do Maven para o Serviço de Aplicativo do Azure

Esta seção ajuda na configuração do projeto Spring Boot para permitir que o aplicativo seja implantado no Serviço de Aplicativo do Azure.

1.  Siga o link para [Configurar o plug-in do Maven para o Serviço de Aplicativo do Azure].
    
    Esse link cria um Serviço de Aplicativo do Azure. Se desejar implantar o aplicativo em um existente, você poderá reconfigurar a implantação por meio do comando `mvn azure-webapp:config` e escolher uma parte do aplicativo para a configuração.
    
    ```bash
    [INFO] Scanning for projects...                                                     
    [INFO]                                                                              
    [INFO] ----------------------< com.wingtiptoys:secrets >-----------------------     
    [INFO] Building secrets 0.0.1-SNAPSHOT                                              
    [INFO] --------------------------------[ jar ]---------------------------------     
    [INFO]                                                                              
    [INFO] --- azure-webapp-maven-plugin:1.9.0:config (default-cli) @ secrets ---       
    Please choose which part to config                                                  
    1. Application                                                                      
    2. Runtime                                                                          
    3. DeploymentSlot                                                                   
    Enter index to use: 1                                                              
    Define value for appName(Default: ********):                                      
    Define value for resourceGroup(Default: ********):                                 
    Define value for region(Default: ********):                                           
    Define value for pricingTier(Default: P1v2):                                        
    1. b1                                                                               
    2. b2                                                                               
    3. b3                                                                               
    4. d1                                                                               
    5. f1                                                                               
    6. p1v2 [*]                                                                         
    7. p2v2                                                                             
    8. p3v2                                                                             
    9. s1                                                                               
    10. s2                                                                              
    11. s3                                                                              
    Enter index to use:                                                                 
    Please confirm webapp properties                                                                                                          
    ```
    
    Você também pode editar diretamente a seção `<configuration>` de `<azure-webapp-maven-plugin>` no *pom.xml*. Altere o `<resourceGroup>`, o `<appName>` e o valor `<region>` para o Serviço de Aplicativo específico.

2. Atribua uma identidade ao Serviço de Aplicativo e desative o `principalId` para a próxima etapa.

   ```bash
   az webapp identity assign --name your-appservice-name \
      --resource-group vged-rg2
   ```
   
3. Conceda permissão para o MSI.

   ```bash
   az keyvault set-policy --name vgedkeyvault \
       --object-id your-managed-identity-objectId \
       --secret-permissions get list
   ```

## <a name="deploy-the-app-to-azure-and-run-app-service"></a>Implantar o aplicativo no Azure e executar o Serviço de Aplicativo

Agora você está pronto para implantar o aplicativo Web no Azure. Para fazer isso, execute as seguintes etapas:

1. Recompile o arquivo JAR usando o Maven se você fez alterações no arquivo *pom.xml*.

   ```bash
   mvn clean package
   ```
   
2. Implante o aplicativo no Azure usando o Maven.

   ```bash
   mvn azure-webapp:deploy
   ```
   
3. Reinicie o Serviço de Aplicativo.

4. Verifique esta URL no navegador: `https://{your-appservice-name}.azurewebsites.net/get` para obter a `connectionString`.
   

## <a name="summary"></a>Resumo

Nesse tutorial, você criou um novo aplicativo Web Java usando o **[Spring Initializr]** , criou um Azure Key Vault para armazenar informações confidenciais, em seguida, configurou o aplicativo para recuperar informações do cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre como usar o Azure Key Vault, consulte os seguintes artigos:

* [Documentação do Key Vault].

* [Introdução ao Cofre da Chave do Azure]

Para obter mais informações sobre como usar aplicativos Spring Boot no Azure, confira os seguintes artigos:

* [Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Executando um Aplicativo Spring Boot em um Cluster Kubernetes no Serviço de Contêiner do Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Para obter mais informações sobre como usar o Azure com Java, confira [Azure para Desenvolvedores Java] e [Como trabalhar com o Java e o Azure DevOps].

Para obter mais informações sobre como usar identidades gerenciadas para o Serviço de Aplicativo, confira [Usar identidades gerenciadas para o Serviço de Aplicativo].

<!-- URL List -->

[Documentação do Key Vault]: /azure/key-vault/
[Introdução ao Cofre da Chave do Azure]: /azure/key-vault/key-vault-get-started
[Azure para desenvolvedores Java]: /azure/developer/java/
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Como trabalhar com o Java e o Azure DevOps]: /azure/devops/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Usar identidades gerenciadas para o Serviço de Aplicativo]: /azure/app-service/overview-managed-identity
[Configurar o plug-in do Maven para o Serviço de Aplicativo do Azure]: /azure/developer/java/spring-framework/deploy-spring-boot-java-app-with-maven-plugin#configure-maven-plugin-for-azure-app-service

<!-- IMG List -->

[secrets-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-01.png
[secrets-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-02.png
[secrets-03]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-03.png

[build-application-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-01.png
[build-application-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-02.png
