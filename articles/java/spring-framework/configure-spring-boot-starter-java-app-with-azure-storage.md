---
title: Como usar o iniciador do Spring Boot para Armazenamento do Azure
description: Saiba como configurar um aplicativo inicializador do Spring Boot com o inicializador do Armazenamento do Azure.
services: storage
documentationcenter: java
ms.date: 12/19/2018
ms.service: storage
ms.topic: article
ms.workload: storage
ms.openlocfilehash: e9546d2e65d198fe9ab92e5d588df8797fd97e16
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81669232"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-storage"></a>Como usar o iniciador do Spring Boot para Armazenamento do Azure

Este artigo explica como criar um aplicativo personalizado usando o **Spring Initializr**, como adicionar o iniciador do armazenamento do Azure ao aplicativo e como usá-lo para carregar um blob em sua conta de armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são obrigatórios para que você siga as etapas neste artigo:

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* A[CLI (interface de linha de comando) do Azure](/cli/azure/index).
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* [Maven](http://maven.apache.org/) do Apache, versão 3.0 ou posterior.

> [!IMPORTANT]
>
> É necessário o Spring Boot versão 2.0 ou posterior para concluir as etapas neste artigo.
>

## <a name="create-an-azure-storage-account-and-blob-container-for-your-application"></a>Criar uma conta do Armazenamento do Azure e um contêiner de blob para o aplicativo

O procedimento a seguir cria uma conta de armazenamento do Azure e um contêiner.

1. Navegue até o portal do Azure em <https://portal.azure.com/> e entre.

1. Clique em **+Criar um recurso**, **Armazenamento** e **Conta de Armazenamento**.

   ![Criar uma Conta de Armazenamento do Azure][IMG01]

1. Na página **Criar conta de armazenamento**, insira as seguintes informações:

   * Selecione a **Assinatura**.
   * Selecione o **Grupo de recursos** ou crie um novo grupo de recursos.
   * Insira um **Nome de conta de armazenamento** exclusivo, que se tornará parte do URI de sua conta de armazenamento. Por exemplo: se você inseriu **wingtiptoysstorage** como o **Nome**, o URI será *wingtiptoysstorage.core.windows.net*.
   * Especifique o **Local** da conta de armazenamento.
1. Após especificar as opções listadas acima, clique em **Revisar + criar**. 
1. Verifique a especificação e clique em **Criar** para criar sua conta de armazenamento.
1. Quando a implantação estiver concluída, selecione **Ir para o recurso**.
1. Clique em **Contêineres**.
1. Clique em **+ Contêiner**.
   * Dê um nome ao contêiner.
   * Selecione *Blob* na lista suspensa.

   ![Criar contêiner de blobs][IMG02]

1. O portal do Azure lista seu contêiner de blob depois que ele for criado.

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Criar um aplicativo Spring Boot simples com o Spring Initializr

O procedimento a seguir cria o aplicativo Spring Boot.

1. Navegue até <https://start.spring.io/>.

1. Especifique as seguintes opções:

   * Gere um projeto **Maven**.
   * Especifique **Java**.
   * Especifique uma versão **Spring Boot** igual ou maior que 2.0.
   * Especifique os nomes de **Grupo** e **Artefato** do aplicativo.
   * Adicione a dependência da **Web**.

      ![Opções básicas do Initializr Basic][SI01]

   > [!NOTE]
   >
   > O Spring Initializr usa os nomes de **Grupo** e **Artefato** para criar o nome do pacote, por exemplo, *com.wintiptoys.storage*.
   >

1. Após especificar as opções listadas acima, clique em **Gerar**.

1. Quando solicitado, baixe o projeto para um caminho no computador local.

1. Após extrair os arquivos no sistema local, seu aplicativo Spring Boot simples estará pronto para edição.

## <a name="configure-your-spring-boot-app-to-use-the-azure-storage-starter"></a>Configurar o aplicativo Spring Boot para usar o Iniciador do Armazenamento do Azure

O procedimento a seguir configura o aplicativo Spring boot para usar o armazenamento do Azure.

1. Localize o arquivo *pom.xml* no diretório-raiz do aplicativo, por exemplo:

   `C:\SpringBoot\storage\pom.xml`

   -ou-

   `/users/example/home/storage/pom.xml`

1. Abra o arquivo *pom.xml* em um editor de texto e adicione o iniciador do Spring Cloud Azure Storage à lista de `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-azure-starter-storage</artifactId>
      <version>1.0.0.M2</version>
   </dependency>
   ```

   ![Editar o arquivo pom.xml][SI03]

1. Salve e feche o arquivo *pom.xml*.

## <a name="create-an-azure-credential-file"></a>Criar um arquivo de credencial do Azure

O procedimento a seguir cria o arquivo de credencial do Azure.

1. Abra um prompt de comando.

1. Navegue até o diretório de *recursos* do aplicativo Spring Boot, por exemplo:

   ```shell
   cd C:\SpringBoot\storage\src\main\resources
   ```

   -ou-

   ```shell
   cd /users/example/home/storage/src/main/resources
   ```

1. Entre na sua conta do Azure:

   ```azurecli
   az login
   ```

1. Liste suas assinaturas:

   ```azurecli
   az account list
   ```
   O Azure retornará uma lista de suas assinaturas, e será preciso copiar o GUID para a assinatura que deseja usar. Por exemplo:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "11111111-1111-1111-1111-111111111111",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "22222222-2222-2222-2222-222222222222",
       "user": {
         "name": "gena.soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. Especifique o GUID para a assinatura que quer usar no Azure; por exemplo:

   ```azurecli
   az account set -s 11111111-1111-1111-1111-111111111111
   ```

1. Crie seu arquivo de credencial do Azure:

   ```azurecli
   az ad sp create-for-rbac --sdk-auth > my.azureauth
   ```

   Esse comando criará um arquivo *my.azureauth* no diretório *recursos* com conteúdo semelhante ao seguinte exemplo:

   ```json
   {
     "clientId": "33333333-3333-3333-3333-333333333333",
     "clientSecret": "44444444-4444-4444-4444-444444444444",
     "subscriptionId": "11111111-1111-1111-1111-111111111111",
     "tenantId": "22222222-2222-2222-2222-222222222222",
     "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
     "resourceManagerEndpointUrl": "https://management.azure.com/",
     "activeDirectoryGraphResourceId": "https://graph.windows.net/",
     "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
     "galleryEndpointUrl": "https://gallery.azure.com/",
     "managementEndpointUrl": "https://management.core.windows.net/"
   }
   ```

## <a name="configure-your-spring-boot-app-to-use-your-azure-storage-account"></a>Configurar o aplicativo Spring Boot para usar sua conta do Armazenamento do Azure

O procedimento a seguir configura o aplicativo Spring boot para usar sua conta de armazenamento do Azure.

1. Localize *application.properties* no diretório *recursos* do aplicativo, por exemplo:

   `C:\SpringBoot\storage\src\main\resources\application.properties`

   -ou-

   `/users/example/home/storage/src/main/resources/application.properties`

2. Abra o arquivo *application.properties* em um editor de texto, adicione as seguintes linhas ao arquivo e substitua os valores de exemplo pelas propriedades adequadas para sua conta de armazenamento:

   ```yaml
   spring.cloud.azure.credential-file-path=my.azureauth
   spring.cloud.azure.resource-group=wingtiptoysresources
   spring.cloud.azure.region=West US
   spring.cloud.azure.storage.account=wingtiptoysstorage
   ```
   Em que:

   |                   Campo                   |                                            Descrição                                            |
   |-------------------------------------------|---------------------------------------------------------------------------------------------------|
   | `spring.cloud.azure.credential-file-path` |            Especifica o arquivo de credencial do Azure que você criou anteriormente neste tutorial.             |
   |    `spring.cloud.azure.resource-group`    |           O nome do grupo de recursos do Azure que contém a conta do Armazenamento do Azure.            |
   |        `spring.cloud.azure.region`        | Especifica a região geográfica que você especificou quando criou a conta do Armazenamento do Azure. |
   |   `spring.cloud.azure.storage.account`    |            Especifica a conta do Armazenamento do Azure que você criou neste tutorial.             |


3. Salve e feche o arquivo *application.properties*.

## <a name="add-sample-code-to-implement-basic-azure-storage-functionality"></a>Adicionar código de exemplo para implementar a funcionalidade básica do armazenamento do Azure

Nesta seção, você criará as classes Java necessárias para armazenar um blob em sua conta de armazenamento do Azure.

### <a name="modify-the-main-application-class"></a>Modificar a classe principal do aplicativo

1. Localize o arquivo Java do aplicativo principal no diretório do pacote do seu aplicativo. Por exemplo:

   `C:\SpringBoot\storage\src\main\java\com\wingtiptoys\storage\StorageApplication.java`

   -ou-

   `/users/example/home/storage/src/main/java/com/wingtiptoys/storage/StorageApplication.java`

1. Abra o arquivo Java do aplicativo principal em um editor de texto e adicione seguintes as linhas ao arquivo. Substitua wingtiptoys pelos seus valores:

   ```java
   package com.wingtiptoys.storage;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class StorageApplication {
      public static void main(String[] args) {
         SpringApplication.run(StorageApplication.class, args);
      }
   }
   ```

1. Salve e feche o arquivo Java do aplicativo principal.

### <a name="add-a-web-controller-class"></a>Adicionar uma classe de controlador da Web

1. Crie um novo arquivo Java chamado *WebController.java* no diretório do pacote do aplicativo; por exemplo:

   `C:\SpringBoot\storage\src\main\java\com\wingtiptoys\storage\WebController.java`

   -ou-

   `/users/example/home/storage/src/main/java/com/wingtiptoys/storage/WebController.java`

1. Abra o arquivo Java do controlador da Web em um editor de texto e adicione as seguintes linhas ao arquivo.  Altere *wingtiptoys* para o seu grupo de recursos e de *armazenamento* para o nome do artefato.

   ```java
   package com.wingtiptoys.storage;

   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.core.io.Resource;
   import org.springframework.core.io.WritableResource;
   import org.springframework.util.StreamUtils;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RestController;

   import java.io.IOException;
   import java.io.OutputStream;
   import java.nio.charset.Charset;

   @RestController
   public class WebController {

      @Value("blob://test/myfile.txt")
      private Resource blobFile;

      @GetMapping(value = "/")
      public String readBlobFile() throws IOException {
         return StreamUtils.copyToString(
            this.blobFile.getInputStream(),
            Charset.defaultCharset()) + "\n";
      }

      @PostMapping(value = "/")
      public String writeBlobFile(@RequestBody String data) throws IOException {
         try (OutputStream os = ((WritableResource) this.blobFile).getOutputStream()) {
            os.write(data.getBytes());
         }
         return "File was updated.\n";
      }
   }
   ```

   Em que a sintaxe `@Value("blob://[container]/[blob]")` define respectivamente os nomes do contêiner e do blob onde você deseja armazenar os dados.

1. Salve e feche o arquivo Java do controlador da Web.

1. Abra um prompt de comando e altere o diretório para a pasta em que seu arquivo *pom.xml* está localizado, por exemplo:

   `cd C:\SpringBoot\storage`

   -ou-

   `cd /users/example/home/storage`

1. Crie seu aplicativo Spring Boot com Maven e execute-o; por exemplo:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Quando seu aplicativo estiver em execução, você poderá usar *curl* para testá-lo, por exemplo:

   a. Envie uma solicitação POST para atualizar o conteúdo do arquivo:

      ```shell
      curl -X POST -H "Content-Type: text/plain" -d "Hello World" http://localhost:8080/
      ```

      Você deverá ver uma resposta indicando que o arquivo foi atualizado.

   b. Envie uma solicitação GET para verificar o conteúdo do arquivo:

      ```shell
      curl -X GET http://localhost:8080/
      ```

     Você deverá ver o texto "Olá, Mundo" publicado.

## <a name="summary"></a>Resumo

Nesse tutorial, você criou um novo aplicativo do Java usando o **[Spring Initializr]** , adicionou o iniciador do armazenamento do Azure ao aplicativo, em seguida, configurou o aplicativo para carregar um blob em sua conta de armazenamento do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](index.yml)

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre os iniciadores adicionais do Spring Boot disponíveis para o Microsoft Azure, consulte [Iniciadores do Spring Boot para o Azure](spring-boot-starters-for-azure.md).

Para obter informações detalhadas sobre APIs adicionais do armazenamento do Azure adicional que podem ser chamadas de seus aplicativos Spring Boot, consulte os seguintes artigos:
* [Como usar o armazenamento do Blob do Azure a partir do Java](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Como usar o armazenamento de Fila do Azure a partir do Java](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Como usar o armazenamento de Tabela do Azure a partir do Java](/azure/cosmos-db/table-storage-how-to-use-java)
* [Como usar o armazenamento de Arquivo do Azure a partir do Java](/azure/storage/files/storage-java-how-to-use-file-storage)

<!-- IMG List -->

[IMG01]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-01.png
[IMG02]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-02.png
[IMG03]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-03.png
[IMG04]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-04.png
[IMG05]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-05.png

[SI01]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-project-01.png
[SI02]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-project-02.png
[SI03]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-project-03.png
