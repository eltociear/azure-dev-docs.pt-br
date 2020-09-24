---
title: Iniciadores do Spring Boot para Azure
description: Este artigo descreve os vários iniciadores do Spring Boot disponíveis para o Azure.
documentationcenter: java
ms.date: 12/19/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 0d4615b3d6b05ee54538befdf6a7a7d220e77917
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831002"
---
# <a name="spring-boot-starters-for-azure"></a>Iniciadores do Spring Boot para Azure

Este artigo descreve os vários iniciadores do Spring Boot do [Spring Initializr] que fornecem aos desenvolvedores de Java os recursos de integração para trabalhar com o Microsoft Azure.

![Iniciadores Spring Boot para Azure][spring-boot-starters]

Os iniciadores do Spring Boot estão disponíveis atualmente para o Azure:

* **[Suporte do Azure](#azure-support)**

   Fornece suporte para configuração automática para serviços do Azure. Por exemplo, Barramento de Serviço, Armazenamento, Active Directory, etc.

* **[Azure Active Directory](#azure-active-directory)**

   Fornece suporte de integração ao Spring Security com o Azure Active Directory para autenticação.

* **[Azure Key Vault](#azure-key-vault)**

   Fornece suporte a Spring valor anotação para integração com os Segredos do Azure Key Vault.

* **[Armazenamento do Azure](#azure-storage)**

   Fornece suporte ao Spring Boot para serviços de Armazenamento do Azure.

<a name="azure-support"></a>
## <a name="azure-support"></a>Suporte do Azure

Este iniciador do Spring Boot fornece suporte de configuração automática para Serviços do Azure. Por exemplo: Barramento de Serviço, Armazenamento, Active Directory, Cosmos DB, o Key Vault, etc.

Para obter exemplos de como usar os vários recursos do Azure que são fornecidos por esse iniciador, consulte:

* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples>

Quando você adiciona esse iniciador a um projeto do Spring Boot, as seguintes alterações são feitas ao arquivo *pom.xml*:

* A propriedade a seguir é adicionada ao elemento `<properties>`:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* A dependência `spring-boot-starter` padrão é substituída pelo seguinte:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-spring-boot</artifactId>
   </dependency>
   ```

* A seção a seguir é adicionada ao arquivo:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-active-directory"></a>
## <a name="azure-active-directory"></a>Azure Active Directory

Esse iniciador do Spring Boot fornece suporte de configuração automática para o Spring Security para fornecer integração com o Active Directory do Azure para autenticação.

Para obter exemplos de como usar os recursos do Azure Active Directory fornecidos por esse iniciador, consulte:

* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-active-directory>

Quando você adiciona esse iniciador a um projeto do Spring Boot, as seguintes alterações são feitas ao arquivo *pom.xml*:

* A propriedade a seguir é adicionada ao elemento `<properties>`:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* A dependência `spring-boot-starter` padrão é substituída pelo seguinte:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-active-directory-spring-boot-starter</artifactId>
   </dependency>
   ```

* A seção a seguir é adicionada ao arquivo:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-key-vault"></a>
## <a name="azure-key-vault"></a>Cofre de Chave do Azure

Esse iniciador do Spring Boot fornece suporte de anotação ao valor do Spring para integração com os Segredos do Azure Key Vault.

Para obter exemplos de como usar os recursos do Azure Key Vault fornecidos por esse iniciador, consulte:

* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-keyvault-secrets>

Quando você adiciona esse iniciador a um projeto do Spring Boot, as seguintes alterações são feitas ao arquivo *pom.xml*:

* A propriedade a seguir é adicionada ao elemento `<properties>`:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* A dependência `spring-boot-starter` padrão é substituída pelo seguinte:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
   </dependency>
   ```

* A seção a seguir é adicionada ao arquivo:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-storage"></a>
## <a name="azure-storage"></a>Armazenamento do Azure

Esse iniciador do Spring Boot fornece suporte à integração do Spring Boot para serviços de Armazenamento do Azure.

Para obter exemplos de como usar os recursos do Armazenamento do Azure fornecidos por esse iniciador, consulte:

* [Como usar o iniciador do Spring Boot para Armazenamento do Azure](configure-spring-boot-starter-java-app-with-azure-storage.md)
* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-storage-blob>

Quando você adiciona esse iniciador a um projeto do Spring Boot, as seguintes alterações são feitas ao arquivo *pom.xml*:

* A propriedade a seguir é adicionada ao elemento `<properties>`:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* A dependência `spring-boot-starter` padrão é substituída pelo seguinte:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-storage-spring-boot-starter</artifactId>
   </dependency>
   ```

* A seção a seguir é adicionada ao arquivo:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](./index.yml)

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre como usar aplicativos [Spring Boot] no Azure, consulte [Spring no Azure].

Para obter mais informações sobre como usar o Azure com Java, confira [Azure para Desenvolvedores Java] e [Como trabalhar com o Java e o Azure DevOps].

Para obter ajuda na introdução a seus próprios aplicativos Spring Boot, confira **Spring Initializr** em https://start.spring.io/.

<!-- URL List -->

[Azure para desenvolvedores Java]: ../index.yml
[Como trabalhar com o Java e o Azure DevOps]: /azure/devops/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring no Azure]: ./index.yml
[Spring Framework]: https://spring.io/
[Spring Initializr]: https://start.spring.io/

<!-- IMG List -->

[spring-boot-starters]: media/spring-boot-starters-for-azure/spring-boot-starters-cropped.png