---
title: Autenticar com os módulos do Gerenciamento do Azure para Node.js
description: Autenticar com uma entidade de serviço para os módulos de gerenciamento do Azure para Node.js
author: kraigb
manager: barbkess
ms.author: kraigb
ms.date: 06/17/2017
ms.topic: article
ms.prod: azure
ms.devlang: nodejs
ms.openlocfilehash: 87a30973c8a295540924e41aee9c8e0af455b41f
ms.sourcegitcommit: 380300c283f3df8a87c7c02635eae3596732fb72
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73661238"
---
# <a name="authenticate-with-the-azure-modules-for-nodejs"></a>Autenticar com os módulos do Azure para Node.js 

Todas as APIs de serviço exigem autenticação por meio de um objeto `credentials` durante a instanciação. Há três maneiras de autenticar e criar as credenciais necessárias por meio do SDK do Azure para Node.js: 

- Autenticação básica
- Logon interativo
- Autenticação de entidade de serviço

## <a name="basic-authentication"></a>Autenticação básica

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