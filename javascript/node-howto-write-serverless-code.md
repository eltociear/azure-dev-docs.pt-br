---
title: Escrever código Node.js sem servidor com o Azure Functions
description: Diretrizes sobre como usar o Azure Functions para criar e implantar código sem servidor.
author: kraigb
manager: barbkess
ms.devlang: nodejs
ms.topic: article
ms.service: azure-nodejs
ms.date: 08/19/2019
ms.author: kraigb
ms.openlocfilehash: 0917da975f29d392944ba2351e894de640182eb9
ms.sourcegitcommit: f519a1ee8017850b2fa37049af3bac1ea5ca5516
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69892490"
---
# <a name="how-to-write-serverless-nodejs-code-on-azure"></a>Como escrever código Node.js sem servidor no Azure

O código sem servidor permite criar pontos de extremidade responsivos e sob demanda na Internet sem precisar se preocupar com o provisionamento ou o gerenciamento de uma infraestrutura. O código sem servidor é composto por scripts ou outros trechos de código executados em resposta a vários eventos. No Azure, a oferta sem servidor é chamada de Azure Functions.

Primeiro, comece com:

- [Criar sua primeira função com o Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code). Este artigo apresenta o Azure Functions no contexto do Visual Studio Code, o que simplifica vários detalhes.

Em seguida, amplie seu conhecimento do que o Azure Functions pode fazer, lendo os seguintes artigos:

- [Uma introdução ao Azure Functions](/azure/azure-functions/functions-overview), que descreve os benefícios do desenvolvimento sem servidor, os custos e os diferentes gatilhos que você pode usar para executar código sem servidor.

- [Conceitos de gatilhos e de associações do Azure Functions](/azure/azure-functions/functions-triggers-bindings)

- [Guia do desenvolvedor do Azure Functions](/azure/azure-functions/functions-reference) seguido pelo [Guia do desenvolvedor de JavaScript do Azure Functions](/azure/azure-functions/functions-reference-node)

- Se você estiver interessado em gravar funções *com estado* em um ambiente sem servidor, leia [O que é o Durable Functions?](/azure/azure-functions/durable/durable-functions-overview), bem como [Criar sua primeira função durável em JavaScript](/azure/azure-functions/durable/quickstart-js-vscode).

Depois disso, você poderá usar diversos outros recursos que ajudam a explorar ainda mais o código sem servidor:

- Módulo do Microsoft Learn: [Habilitar atualizações automáticas em um aplicativo Web usando o Azure Functions e o Serviço do SignalR](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr/)

- Saiba mais sobre como usar vários gatilhos para executar código sem servidor:

  - [Executar código em um temporizador](/azure/azure-functions/functions-create-scheduled-function)
  - [Executar código quando um arquivo é carregado ou atualizado no Armazenamento de Blobs do Azure](/azure/storage/blobs/storage-upload-process-images?tabs=nodejsv10)
  - [Executar código quando uma mensagem é escrita no Armazenamento de Filas do Azure](/azure/azure-functions/functions-create-storage-queue-triggered-function)

- [Armazenar dados não estruturados usando o Azure Functions e o Azure Cosmos DB](/azure/azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md?tabs=javascript). Para saber mais sobre outros bancos de dados, confira [Como integrar bancos de dados do Azure em código Node.js](node-howto-integrate-databases.md)

- [Codificar e testar o Azure Functions localmente](/azure/azure-functions/functions-develop-local) junto com [Estratégias para testar seu código no Azure Functions](/azure/azure-functions/functions-test-a-function) e [Tratamento de erros](/azure/azure-functions/functions-bindings-error-pages)

- [Configurar a autenticação no Azure Active Directory](/azure/app-service/configure-authentication-provider-aad.md?toc=%2fazure%2fazure-functions%2ftoc.json)

- [Configurar a implantação contínua no Azure Pipelines](/azure/azure-functions/functions-how-to-azure-devops)

- [Exemplos de Node.js + Azure Functions](/samples/browse/?languages=javascript%2Cnodejs&products=azure-functions)
