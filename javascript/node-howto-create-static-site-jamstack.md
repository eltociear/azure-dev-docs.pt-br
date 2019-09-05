---
title: Criar sites estáticos com o Node.js no Azure
description: Como usar o Azure para criar um aplicativo JAMstack (JavaScript, APIs e Markup)
author: kraigb
manager: barbkess
ms.devlang: nodejs
ms.topic: article
ms.service: azure-nodejs
ms.date: 08/20/2019
ms.author: kraigb
ms.openlocfilehash: ed0e922c2d18a35b12c09533fcf1444487d0e95c
ms.sourcegitcommit: f519a1ee8017850b2fa37049af3bac1ea5ca5516
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69892450"
---
# <a name="how-to-build-jamstack-static-site-web-apps-with-azure"></a>Como criar aplicativos Web JAMstack (site estático) com o Azure

Ótimos aplicativos Web podem ser criados e mantidos de modo produtivo por meio de uma combinação de front-end do *JavaScript*, *APIs* (APIs personalizadas ou de terceiros criadas como código sem servidor) e *markup* com modelo (HTML e CSS) exibida como páginas estáticas. Com essa combinação, também conhecida como JAMstack, você evita escrever código de back-end complexo para exibir páginas da Web. Em vez disso, o sistema exibe somente páginas estáticas (HTML, CSS e JavaScript), que chamam as APIs para o trabalho no lado do servidor. Como é possível gravar essas APIs com tecnologias sem servidor de dimensionamento automático, você evita completamente as preocupações de custo e segurança de usar servidores Always On normais ou hosts da Web. Para saber mais, acesse [jamstack.org](https://jamstack.org/).

Para implementar um site estático/JAMstack no Azure, você usará uma variedade de ferramentas e serviços:

- Configure um banco de dados conforme o necessário.
- Implemente código de API sem servidor no Azure Functions. Normalmente, essas APIs usam o banco de dados.
- Escolha as bibliotecas desejadas para desenvolvimento de front-end, como a Angular. Em seguida, faça upload desses arquivos de HTML, CSS e JavaScript estático para o Armazenamento de Blobs do Azure, que fornece um servidor Web interno.
- Crie um proxy reverso de modo que todo o tráfego passe por um domínio de URL.

É possível assistir a uma demonstração do processo com a sessão //build 2019, [Desenvolvimento produtivo de front-end com JavaScript, Visual Studio Code e Azure](https://mybuild.techcommunity.microsoft.com/sessions/77038?source=sessions#top-anchor).

> [!VIDEO https://medius.studios.ms/Embed/Video-nc/B19-BRK3021?latestplayer=true]

Confira um tutorial passo a passo em [Implantar um site estático no Azure](https://code.visualstudio.com/tutorials/static-website/getting-started) na documentação do Visual Studio Code.

Os artigos a seguir também explicam mais detalhes:

- **Bancos de dados**: é possível usar qualquer banco de dados, como os diferentes serviços de banco de dados do Azure descritos em [Como integrar bancos de dados do Azure em aplicativos do Node.js](node-howto-integrate-databases.md).
  
- **APIs sem servidor**:

  - Comece com [Criar sua primeira função usando o Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code), que apresenta o Azure Functions no contexto do Visual Studio Code, o que simplifica muitos dos detalhes.
  - Ao concluir o artigo, você tem um projeto (uma pasta) do Azure Functions que contém uma subpasta com o nome da função, que é a mesma do ponto de extremidade HTTP. Essa pasta da função contém um arquivo *index.js* com o código.
  - É possível modificar essa função conforme o necessário. Você também pode adicionar mais funções ao projeto e, em seguida, implantá-las novamente no Azure, onde elas ficam publicamente disponíveis.
  - Para ter mais recursos de desenvolvimento sem servidor, confira [Como escrever código Node.js sem servidor no Azure](node-howto-write-serverless-code.md)

- **Implantar o front-end no Armazenamento do Azure**: com as APIs, agora você pode escrever o código de front-end para usar essas APIs, por meio de qualquer estrutura que desejar. Quando estiver pronto, siga o artigo [Tutorial: Hospedar um site estático no Armazenamento de Blobs](/azure/storage/blobs/storage-blob-static-website-host), para fazer upload desses arquivos no Azure e ativar a hospedagem de sites estáticos.

- **Criar um proxy reverso**: Um proxy reverso, conforme descrito em [Trabalhar com proxies do Azure Functions](/azure/azure-functions/functions-proxies), permite direcionar facilmente determinadas solicitações a URLs diferentes. Nesse caso, você quer direcionar solicitações dos arquivos de front-end para a URL do Armazenamento do Azure em que os arquivos foram implantados e as solicitações de API para a URL do Azure Functions.

  - Para criar esses proxies, edite o arquivo *proxies.json* no projeto do Functions project de modo que ele apareça como mostrado abaixo, substituindo as URLs por `<storage_url>` e `<api_url>`:
  
    ```json
    {
      "$schema": "http://json.schemastore.org/proxies",
      "proxies": {
        "Static frontend on Azure Storage": {
          "matchCondition": {
            "route": "/{*restOfPath}"
          },
          "backendUri": "<storage_url>/{restOfPath}"
        },
        "Azure Functions API": {
          "matchCondition": {
            "route": "/api/{*restOfPath}"
          },
          "backendUri": "<api_url>/api/{restOfPath}"
        }
      }
    }
    ```