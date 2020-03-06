---
title: Implantar contêineres do Docker no Serviço de Aplicativo do Azure usando o Visual Studio Code
description: Parte 1 do tutorial, introdução e pré-requisitos.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 98ebb6bd857216cb395eb8cc62faa3d6ee30e3eb
ms.sourcegitcommit: aa2c66b0fecce51862cc9115f68d39c770f0b2ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77709824"
---
# <a name="deploy-containers-to-azure-app-service"></a>Implantar contêineres no Serviço de Aplicativo do Azure

Neste tutorial, você usa o Visual Studio Code para criar um aplicativo Node.js em contêiner usando o Docker, enviar por push a imagem de contêiner para um Registro e então implantar a imagem no Serviço de Aplicativo do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [assinatura do Azure](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão do Docker](vscode:extension/ms-azuretools.vscode-docker)
- A [extensão do Serviço de Aplicativo do Azure](vscode:extension/ms-azuretools.vscode-azureappservice)
- [Node.js e npm](https://nodejs.org/en/download), o gerenciador de pacotes do Node.js.
- [Docker](https://www.docker.com/community-edition).

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-docker">Instalar a extensão do Docker</a>

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-azureappservice">Instalar a extensão do Serviço de Aplicativo do Azure</a>

### <a name="azure-subscription"></a>Assinatura do Azure

Se você ainda não tiver uma assinatura do Azure, [inscreva-se agora](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) para obter uma conta gratuita com US$ 200 em créditos Azure a fim de experimentar qualquer combinação de serviços.

## <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

## <a name="verify-docker-install"></a>Verificar a instalação do Docker

Verifique se você tem o Docker instalado corretamente executando o seguinte comando em um terminal ou em um prompt de comando:

```bash
docker --version
```

A saída deve ser parecida com o seguinte:

<pre>
Docker Version 17.12.0-ce, build c97c6d6
</pre>

> [!div class="nextstepaction"]
> [Instalei a extensão do Docker](tutorial-vscode-docker-node-02.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=getting-started)
