---
title: Bibliotecas de gerenciamento do Azure para exemplos de aplicativo Web de Java
description: Obter o código de exemplo para criação e atualização de aplicativos Web do Azure hospedados no Serviço de Aplicativo usando as bibliotecas de gerenciamento do Azure para Java
keywords: Azure, Java, SDK, API, Maven, Gradle, aplicativos Web, serviço de aplicativo
author: rloutlaw
ms.date: 04/16/2017
ms.topic: article
ms.service: multiple
ms.assetid: 43633e5c-9fb1-4807-ba63-e24c126754e2
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 585cb33b6eabc5e353ddda37f687bcf0bacc6901
ms.sourcegitcommit: c6642cae6fdb5e3025ed66fcd4ef89792c3b436a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2020
ms.locfileid: "86405697"
---
# <a name="azure-management-libraries-for-java---web-app-samples"></a>Bibliotecas de gerenciamento do Azure para Java – exemplos de aplicativo Web 

A tabela a seguir contém links para código-fonte em Java que você pode usar para criar e configurar aplicativos Web.

| Amostra | Descrição |
|---|---|
| **Criar um aplicativo** ||
| [Criar um aplicativo Web e implantar do FTP ou GitHub][1] | Implantar aplicativos Web do Git local, FTP e integração contínua do GitHub. |
| [Criar um aplicativo Web e gerenciar slots de implantação][2] | Criar um aplicativo Web e implantar para slots de preparo e, em seguida, alternar as implantações entre os slots. |
| **Configurar um aplicativo** ||
| [Criar um aplicativo Web e configurar um domínio personalizado][3] | Criar um aplicativo Web com um domínio personalizado e o certificado SSL autoassinado. |
| **Dimensionar um aplicativo** ||
| [Dimensionar um aplicativo Web com alta disponibilidade em várias regiões][4] | Criar um aplicativo Web em três regiões geográficas diferentes e os disponibilizar por meio de um único ponto de extremidade usando o Gerenciador de Tráfego do Azure. | 
| **Conectar um aplicativo aos recursos** ||
| [Como conectar um aplicativo Web a uma conta de armazenamento][5] | Criar uma conta de armazenamento do Azure e adicionar a cadeia de conexão de armazenamento às configurações do aplicativo. |
| [Conectar um aplicativo web a um banco de dados SQL][6] | Criar um aplicativo Web e um Banco de Dados SQL e, em seguida, adicionar a cadeia de conexão do banco de dados às configurações do aplicativo. |

[1]: java-sdk-configure-webapp-sources.md
[2]: https://github.com/Azure-Samples/app-service-java-manage-staging-and-production-slots-for-web-apps/
[3]: https://github.com/Azure-Samples/app-service-java-manage-web-apps-with-custom-domains/
[4]: https://azure.microsoft.com/resources/samples/app-service-java-scale-web-apps-on-linux/
[5]: https://github.com/Azure-Samples/app-service-java-manage-storage-connections-for-web-apps/
[6]: https://github.com/Azure-Samples/app-service-java-manage-data-connections-for-web-apps/