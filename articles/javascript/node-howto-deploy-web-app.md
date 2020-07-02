---
title: Implantar aplicativos Web do Node.js no Azure
description: Introdução ao Serviço de Aplicativo do Azure e outras opções de hospedagem para aplicativos Web, incluindo PWA (aplicativos Web progressivos)
ms.topic: article
ms.date: 08/20/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: e7cb374b645738140ead924296ef7cef9b1b61d7
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85791120"
---
# <a name="deploy-nodejs-web-apps-to-azure-app-service"></a>Implantar aplicativos Web do Node.js no Serviço de Aplicativo do Azure

No Azure, há várias opções para implantar e hospedar aplicativos Web:

- A melhor opção de hospedagem para aplicativos Web é o Serviço de Aplicativo do Azure, uma oferta de PaaS (plataforma como serviço). Para começar, experimente um dos seguintes recursos:

  - [Criar um aplicativo Web do Node.js no Azure](/azure/app-service/app-service-web-get-started-nodejs)
  - [Experimente o Serviço de Aplicativo do Azure – Crie um aplicativo Expresso com base em um modelo](https://code.visualstudio.com/tryappservice/?utm_source=msftdocs&utm_medium=microsoft&utm_campaign=tryappservice)
  - [Hospedar um aplicativo Web com o Serviço de Aplicativo do Azure – Módulo do Learn](/learn/modules/host-a-web-app-with-azure-app-service/index)
  - [Compilar um aplicativo Node.js e MongoDB no Azure](/azure/app-service/app-service-web-tutorial-nodejs-mongodb-app)
  - [Exemplos do Serviço de Aplicativo](/samples/browse/?languages=javascript%2Cnodejs&products=azure-app-service)

- É possível criar contêineres próprios e implantá-los no Azure por meio do Registro de Contêiner do Azure e do Serviço de Kubernetes do Azure. Para saber detalhes, confira [Como implantar contêineres do Node.js no Azure](node-howto-deploy-containers.md).

- Caso queira trabalhar principalmente com um código sem servidor, confira [Como escrever um código Node.js sem servidor no Azure](node-howto-write-serverless-code.md).

- Para saber detalhes sobre como criar um site JAMstack (estático), confira [Como criar aplicativos Web JAMstack (site estático) com o Azure](node-howto-create-static-site-jamstack.md).

- Se você quiser controlar a infraestrutura, poderá simplesmente usar uma máquina virtual. Para começar, siga o caminho [Implantar um site com as Máquinas Virtuais do Azure](/learn/paths/deploy-a-website-with-azure-virtual-machines/) no Microsoft Learn.

Para ter uma visão geral completa das diferentes opções, confira [Árvore de decisão para os serviços de computação do Azure](/azure/architecture/guide/technology-choices/compute-decision-tree), bem como o módulo [Principais Serviços de Nuvem – Opções de computação do Azure](/learn/modules/intro-to-azure-compute/) no Microsoft Learn.
