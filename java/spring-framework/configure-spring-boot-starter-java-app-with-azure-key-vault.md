---
title: Como usar o iniciador do Spring Boot para o Azure Key Vault
description: Descubra como configurar um aplicativo inicializador do Spring Boot com o iniciador do Azure Key Vault.
services: key-vault
documentationcenter: java
author: bmitchell287
manager: douge
editor: ''
ms.assetid: ''
ms.author: brendm
ms.date: 12/19/2018
ms.devlang: java
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: 1c04bab67c7fc6a409893416d27de7ed18018cd9
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68283217"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-key-vault"></a>Como usar o iniciador do Spring Boot para o Azure Key Vault

## <a name="overview"></a>Visão geral

Este artigo demonstra como criar um aplicativo com o **[Spring Initializr]** , o qual usa o iniciador do Spring Boot para o Azure Key Vault para recuperar uma cadeia de conexão armazenada como um segredo em um cofre de chaves.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são obrigatórios para você concluir as etapas neste artigo:

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN] ou inscrever-se para uma [conta gratuita do Azure].
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) versão 3.0 ou posterior.

## <a name="create-an-app-using-spring-initializr"></a>Criar um aplicativo usando o Spring Initialzr

1. Navegue até <https://start.spring.io/>.

1. Especifique que você deseja gerar um projeto **Maven** com **Java**, insira os nomes de **Grupo** e **Artefato** para o seu aplicativo e, em seguida, clique no link para **Alternar para a versão completa** do Spring Initializr.

   ![Especificar os nomes do grupo e do artefato][secrets-01]

1. Role para baixo, até a seção **Azure** e marque a caixa do **Azure Key Vault**.

   ![Selecionar o iniciador do Azure Key Vault][secrets-02]

1. Role até a parte inferior da página e clique no botão **Gerar Projeto**.

   ![Gerar projeto do Spring Boot][secrets-03]

1. Quando solicitado, baixe o projeto para um caminho no computador local.

## <a name="sign-into-azure"></a>Entrar no Azure

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

1. Crie um grupo de recursos para os recursos do Azure que serão usados no seu cofre de chaves. Por exemplo:
   ```azurecli
   az group create --name wingtiptoysresources --location westus
   ```
   Em que:

   | Parâmetro | Descrição |
   |---|---|
   | `name` | Especifica um nome exclusivo para o grupo de recursos. |
   | `location` | Especifica a [região do Azure](https://azure.microsoft.com/regions/) na qual seu grupo de recursos será hospedado. |

   A CLI do Azure exibirá os resultados da criação do grupo de recursos, por exemplo:  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/resourceGroups/wingtiptoysresources",
     "location": "westus",
     "managedBy": null,
     "name": "wingtiptoysresources",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```

2. Crie uma entidade de serviço do Azure a partir do registro do seu aplicativo. Por exemplo:
   ```shell
   az ad sp create-for-rbac --name "wingtiptoysuser"
   ```
   Em que:

   | Parâmetro | DESCRIÇÃO |
   |---|---|
   | `name` | Especifica o nome para a entidade de serviço do Azure. |

   A CLI do Azure retornará uma mensagem de status do JSON que contém o *appId* e a *senha*, o que será usado posteriormente como a ID e senha do cliente. Por exemplo:

   ```json
   {
     "appId": "iiiiiiii-iiii-iiii-iiii-iiiiiiiiiiii",
     "displayName": "wingtiptoysuser",
     "name": "http://wingtiptoysuser",
     "password": "pppppppp-pppp-pppp-pppp-pppppppppppp",
     "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

3. Crie um novo cofre de chaves no grupo de recursos. Por exemplo:
   ```azurecli
   az keyvault create --name wingtiptoyskeyvault --resource-group wingtiptoysresources --location westus --enabled-for-deployment true --enabled-for-disk-encryption true --enabled-for-template-deployment true --sku standard --query properties.vaultUri
   ```
   Em que:

   | Parâmetro | Descrição |
   |---|---|
   | `name` | Especifica um nome exclusivo para o seu cofre de chaves. |
   | `location` | Especifica a [região do Azure](https://azure.microsoft.com/regions/) na qual seu grupo de recursos será hospedado. |
   | `enabled-for-deployment` | Especifica a [opção de implantação do cofre de chaves](/cli/azure/keyvault). |
   | `enabled-for-disk-encryption` | Especifica a [opção de criptografia do cofre de chaves](/cli/azure/keyvault). |
   | `enabled-for-template-deployment` | Especifica a [opção de criptografia do cofre de chaves](/cli/azure/keyvault). |
   | `sku` | Especifica a [opção de SKU do cofre de chaves](/cli/azure/keyvault). |
   | `query` | Especifica um valor a ser recuperado da resposta, que é o URI do cofre de chaves que será necessário para concluir este tutorial. |

   A CLI do Azure exibirá o URI do cofre de chaves, que será usado posteriormente. Por exemplo:  

   ```
   "https://wingtiptoyskeyvault.vault.azure.net"
   ```

4. Defina a política de acesso da entidade de serviço do Azure criada anteriormente. Por exemplo:
   ```azurecli
   az keyvault set-policy --name wingtiptoyskeyvault --secret-permission set get list delete --spn "iiiiiiii-iiii-iiii-iiii-iiiiiiiiiiii"
   ```
   Em que:

   | Parâmetro | Descrição |
   |---|---|
   | `name` | Especifica o nome do cofre de chaves de antes. |
   | `secret-permission` | Especifica as [políticas de segurança](/cli/azure/keyvault) do seu cofre de chaves. |
   | `spn` | Especifica o GUID do seu registro de aplicativo de antes. |

   A CLI do Azure exibirá os resultados da criação da política de segurança. Por exemplo:  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/...",
     "location": "westus",
     "name": "wingtiptoyskeyvault",
     "properties": {
       ...
       ... (A long list of values will be displayed here.)
       ...
     },
     "resourceGroup": "wingtiptoysresources",
     "tags": {},
     "type": "Microsoft.KeyVault/vaults"
   }
   ```

5. Armazenar um segredo no seu novo cofre de chaves. Por exemplo:
   ```azurecli
   az keyvault secret set --vault-name "wingtiptoyskeyvault" --name "connectionString" --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
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
     "id": "https://wingtiptoyskeyvault.vault.azure.net/secrets/connectionString/123456789abcdef123456789abcdef",
     "kid": null,
     "managed": null,
     "tags": {
       "file-encoding": "utf-8"
     },
     "value": "jdbc:sqlserver://wingtiptoys.database.windows.net:1433;database=DATABASE;"
   }
   ```

## <a name="configure-and-compile-your-app"></a>Configurar e compilar seu aplicativo

1. Extraia os arquivos do projeto Spring Boot que você baixou anteriormente em um diretório.

2. Navegue até a pasta *src/main/resources* no seu projeto e abra o arquivo *application.properties* no editor de texto.

3. Adicione os valores do cofre de chaves usando os valores obtidos nas etapas concluídas anteriormente neste tutorial. Por exemplo:
   ```yaml
   azure.keyvault.uri=https://wingtiptoyskeyvault.vault.azure.net/
   azure.keyvault.client-id=iiiiiiii-iiii-iiii-iiii-iiiiiiiiiiii
   azure.keyvault.client-key=pppppppp-pppp-pppp-pppp-pppppppppppp
   ```
   Em que:

   |          Parâmetro          |                                 Descrição                                 |
   |-----------------------------|-----------------------------------------------------------------------------|
   |    `azure.keyvault.uri`     |           Especifica o URI de quando você criou seu cofre de chaves.           |
   | `azure.keyvault.client-id`  |  Especifica o GUID da *appId* de quando você criou a entidade de serviço.   |
   | `azure.keyvault.client-key` | Especifica o GUID da *senha* de quando você criou a entidade de serviço. |


4. Navegue até o arquivo de código-fonte principal do projeto. Por exemplo: */src/main/java/com/wingtiptoys/secrets*.

5. Abra o arquivo principal Java do aplicativo em um arquivo em um editor de texto; por exemplo: *SecretsApplication.java* e adicione as seguintes linhas ao arquivo:

   ```java
   package com.wingtiptoys.secrets;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.boot.CommandLineRunner;

   @SpringBootApplication
   public class SecretsApplication implements CommandLineRunner {

      @Value("${connectionString}")
      private String connectionString;

      public static void main(String[] args) {
         SpringApplication.run(SecretsApplication.class, args);
      }

      public void run(String... varl) throws Exception {
         System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
      }
   }
   ```
   Este exemplo de código recupera a cadeia de conexão do cofre de chaves e a exibe à linha de comando.

6. Salve e feche o arquivo Java.

## <a name="build-and-test-your-app"></a>Crie e testar seu aplicativo

1. Navegue até o diretório no qual o arquivo *pom.xml* do seu aplicativo Spring Boot está localizado:

1. Crie seu aplicativo Spring Boot com o Maven. Por exemplo:

   ```bash
   mvn clean package
   ```

   O Maven exibirá os resultados de seu build.

   ![Status do build do aplicativo Spring Boot][build-application-01]

1. Execute o aplicativo Spring Boot com o Maven. O aplicativo exibirá a cadeia de conexão do seu cofre de chaves. Por exemplo:

   ```bash
   mvn spring-boot:run
   ```

   ![Mensagem do tempo de execução do Spring Boot][build-application-02]

## <a name="summary"></a>Resumo

Nesse tutorial, você criou um novo aplicativo Web Java usando o **[Spring Initializr]** , criou um Azure Key Vault para armazenar informações confidenciais, em seguida, configurou o aplicativo para recuperar informações do cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre como usar o Azure Key Vault, consulte os seguintes artigos:

* [Documentação do Key Vault].

* [Introdução ao Cofre da Chave do Azure]

Para obter mais informações sobre como usar aplicativos Spring Boot no Azure, confira os seguintes artigos:

* [Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Executando um Aplicativo Spring Boot em um Cluster Kubernetes no Serviço de Contêiner do Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Para obter mais informações sobre como usar o Azure com Java, confira [Azure para Desenvolvedores Java] e [Como trabalhar com o Java e o Azure DevOps].

<!-- URL List -->

[Documentação do Key Vault]: /azure/key-vault/
[Introdução ao Cofre da Chave do Azure]: /azure/key-vault/key-vault-get-started
[Azure para desenvolvedores Java]: /azure/java/
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Como trabalhar com o Java e o Azure DevOps]: /azure/devops/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[secrets-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-01.png
[secrets-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-02.png
[secrets-03]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-03.png

[build-application-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-01.png
[build-application-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-02.png
