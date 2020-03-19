---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: b2458a80eccfcae24a3364208334cce3aedea861
ms.sourcegitcommit: 21ddeb9bd9abd419d143dc2ca8a7c821a1758cf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129071"
---
### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>Determinar se as Filas ou os Tópicos JMS estão em uso

Se o aplicativo estiver usando Filas ou Tópicos JMS, você precisará migrá-los para um servidor JMS hospedado externamente, como o Barramento de Serviço do Azure; confira [Usar o Barramento de Serviço como agente de mensagens](/azure/service-bus-messaging/message-transfers-locks-settlement).

Se os armazenamentos persistentes JMS tiverem sido configurados, você deverá capturar a configuração deles e aplicá-la após a migração.
