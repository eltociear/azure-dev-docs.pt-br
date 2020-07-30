---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 510218bcbed53dfb4383b6a906911790f7865975
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401311"
---
Ele permite que você registre e cancele o registro de um hub de notificação por meio do serviço de back-end que você criou. 

Um alerta é exibido quando uma ação é especificada e o aplicativo está em primeiro plano. Caso contrário, as notificações aparecem no centro de notificações.

> [!NOTE]
> Normalmente, você executaria as ações de registro (e cancelamento de registro) durante o ponto adequado no ciclo de vida do aplicativo (ou como parte da experiência de primeira execução, talvez) sem entradas explícitas de registro/cancelamento de registro do usuário. No entanto, este exemplo exigirá uma entrada de usuário explícita para permitir que essa funcionalidade seja explorada e testada com mais facilidade.
