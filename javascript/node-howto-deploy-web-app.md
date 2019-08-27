---
title: Implantar aplicativos Web do Node.js no Azure
description: Introdução ao Serviço de Aplicativo do Azure e outras opções de hospedagem para aplicativos Web, incluindo PWA (aplicativos Web progressivos)
author: kraigb
manager: barbkess
ms.devlang: nodejs
ms.topic: article
ms.service: azure-nodejs
ms.date: 08/20/2019
ms.author: kraigb
ms.openlocfilehash: c58cd1ed3480fae88d9839b44f0f4062c825a068
ms.sourcegitcommit: f519a1ee8017850b2fa37049af3bac1ea5ca5516
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69892340"
---
# <a name="how-to-deploy-web-apps-to-azure"></a>Como implantar aplicativos Web no Azure

No Azure, há várias opções para hospedar aplicativos Web:

- A melhor opção de hospedagem para aplicativos Web é o Serviço de Aplicativo do Azure, uma oferta de PaaS (plataforma como serviço). Para começar, experimente um dos seguintes recursos:

  - [Criar um aplicativo Web do Node.js no Azure](/azure/app-service/app-service-web-get-started-nodejs)
  - [Experimente o Serviço de Aplicativo do Azure – Crie um aplicativo Expresso com base em um modelo](https://code.visualstudio.com/tryappservice/?utm_source=msftdocs&utm_medium=microsoft&utm_campaign=tryappservice)
  - [Hospedar um aplicativo Web com o Serviço de Aplicativo do Azure – Módulo do Learn](/learn/modules/host-a-web-app-with-azure-app-service/index)
  - [Compilar um aplicativo Node.js e MongoDB no Azure](/azure/app-service/app-service-web-tutorial-nodejs-mongodb-app)
  - [Exemplos do Serviço de Aplicativo](/samples/browse/?languages=javascript%2Cnodejs&products=azure-app-service)

- É possível criar contêineres próprios e implantá-los no Azure por meio do Registro de Contêiner do Azure e do Serviço de Kubernetes do Azure. Para saber detalhes, confira [Como implantar contêineres do Node.js no Azure](node-howto-deploy-containers.md).

- Se você gosta de trabalhar principalmente com código sem servidor, confira [Como escrever código sem servidor do Node.js no Azure](node-howto-write-serverless-code.md).

- Para saber detalhes sobre como criar um site JAMstack (estático), confira [Como criar aplicativos Web JAMstack (site estático) com o Azure](node-howto-create-static-site-jamstack.md).

- Se você quiser controlar a infraestrutura, poderá simplesmente usar uma máquina virtual. Para começar, siga o caminho [Implantar um site com as Máquinas Virtuais do Azure](/learn/paths/deploy-a-website-with-azure-virtual-machines/) no Microsoft Learn.

Para ter uma visão geral completa das diferentes opções, confira [Árvore de decisão para os serviços de computação do Azure](/azure/architecture/guide/technology-choices/compute-decision-tree), bem como o módulo [Principais Serviços de Nuvem – Opções de computação do Azure](/learn/modules/intro-to-azure-compute/) no Microsoft Learn.
