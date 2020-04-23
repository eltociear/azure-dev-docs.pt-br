---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 69bddfce67388d3392e6908f3ddf1af378b116cf
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673482"
---
### <a name="determine-whether-java-message-service-jms-queues-or-topics-are-in-use"></a>Determinar se as Filas ou os Tópicos do JMS (Java Message Service) estão em uso

Se seu aplicativo estiver usando Filas ou Tópicos JMS, você precisará migrá-los para um servidor JMS hospedado externamente. O Barramento de Serviço do Azure e o Advanced Message Queuing Protocol podem ser uma excelente estratégia de migração para pessoas que usam JMS. Para obter mais informações, consulte [Usar o JMS com o Barramento de Serviço do Azure e o AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

Se os armazenamentos persistentes JMS tiverem sido configurados, você deverá capturar a configuração deles e aplicá-la após a migração.

Se você estiver usando o Oracle Message Broker, poderá migrar esse software para as máquinas virtuais do Azure e usá-lo no estado em que se encontra.
