---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 6d9f12534c8bd5ab4fac8a1e337196fcc7ad559e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401327"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>Configurar seu hub de notificação com as informações de APNS

Em **Serviços de Notificação**, selecione **Apple** e siga as etapas apropriadas com base na abordagem que você selecionou antes na seção [Criar um certificado para os Hubs de Notificação](#creating-a-certificate-for-notification-hubs).  

> [!NOTE]
> Use **Produção** no **Modo de Aplicativo** somente caso queira enviar notificações por push aos usuários que tenham comprado seu aplicativo da loja.

### <a name="option-1-using-a-p12-push-certificate"></a>OPÇÃO UM: Usando um certificado push .p12

1. Selecione **Certificado**.

1. Selecione o ícone arquivo.

1. Selecione o arquivo .p12 que você exportou anteriormente e, em seguida, clique em **Abrir**.

1. Se necessário, especifique a senha correta.

1. Selecione o modo **Sandbox**.

1. Clique em **Salvar**.

### <a name="option-2-using-token-based-authentication"></a>OPÇÃO DOIS: Usando a autenticação baseada em token

1. Selecione **Token**.
1. Insira os seguintes valores que você adquiriu antes:

    - **ID da Chave**
    - **ID do Pacote**
    - **ID da Equipe**
    - **Token**

1. Selecione **Área restrita**.
1. Clique em **Salvar**.
