---
title: Autenticar com os módulos do Gerenciamento do Azure para Node.js
description: Autenticar com uma entidade de serviço para os módulos de gerenciamento do Azure para Node.js
ms.topic: article
ms.date: 06/17/2017
ms.custom: devx-track-javascript
ms.openlocfilehash: 1d3f0d2930d397c24177f0cee7a9e276c4df9d67
ms.sourcegitcommit: b03cb337db8a35e6e62b063c347891e44a8a5a13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91110421"
---
# <a name="authenticate-with-the-azure-modules-for-nodejs"></a>Autenticar com os módulos do Azure para Node.js

Todas as APIs de serviço exigem autenticação por meio de um objeto `credentials` durante a instanciação. Há três maneiras de autenticar e criar as credenciais necessárias por meio do SDK do Azure para Node.js:

- Autenticação Básica
- Logon interativo
- Autenticação de entidade de serviço

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="basic-authentication"></a>Autenticação Básica

Para autenticar programaticamente usando as credenciais da sua conta do Azure, use a função `loginWithUsernamePassword`. O snippet de código JavaScript a seguir ilustra como usar a autenticação básica usando as credenciais armazenadas como variáveis de ambiente.

```javascript
const Azure = require('azure');
const MsRest = require('ms-rest-azure');

MsRest.loginWithUsernamePassword(process.env.AZURE_USER,
                                 process.env.AZURE_PASS,
                                 (err, credentials) => {
  if (err) throw err;

  let storageClient = Azure.createARMStorageManagementClient(credentials,
                                                             '<azure-subscription-id>');

  // ..use the client instance to manage service resources.
});
```

## <a name="interactive-login"></a>Logon interativo

O logon interativo fornece um link e um código que permitem que o usuário se autentique de um navegador. Use esse método quando várias contas forem usadas pelo mesmo script ou quando a intervenção do usuário for preferencial.

```javascript
const Azure = require('azure');
const MsRest = require('ms-rest-azure');

MsRest.interactiveLogin((err, credentials) => {
  if (err) throw err;

  let storageClient = Azure.createARMStorageManagementClient(credentials, '<azure-subscription-id>');

  // ..use the client instance to manage service resources.
});
```

## <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

[Logon interativo](#interactive-login) é a maneira mais fácil de autenticar. No entanto, ao usar o SDK do Node.js, convém usar a autenticação da entidade de serviço em vez de fornecer suas credenciais de conta. O tópico [Criar uma entidade de serviço do Azure com Node.js](./node-sdk-azure-authenticate-principal.md) explica várias técnicas para criar (e usar) uma entidade de serviço.

## <a name="next-steps"></a>Próximas etapas

* [Implantar um site estático no Azure usando o Visual Studio Code](tutorial-vscode-static-website-node-01.md)