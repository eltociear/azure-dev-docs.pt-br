---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 69bddfce67388d3392e6908f3ddf1af378b116cf
ms.sourcegitcommit: 951fc116a9519577b5d35b6fb584abee6ae72b0f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624127"
---
### <a name="determine-whether-java-message-service-jms-queues-or-topics-are-in-use"></a>Determinar se as Filas ou os Tópicos do JMS (Java Message Service) estão em uso

Se seu aplicativo estiver usando Filas ou Tópicos JMS, você precisará migrá-los para um servidor JMS hospedado externamente. O Barramento de Serviço do Azure e o Advanced Message Queuing Protocol podem ser uma excelente estratégia de migração para pessoas que usam JMS. Para obter mais informações, consulte [Usar o JMS com o Barramento de Serviço do Azure e o AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

Se os armazenamentos persistentes JMS tiverem sido configurados, você deverá capturar a configuração deles e aplicá-la após a migração.

Se você estiver usando o Oracle Message Broker, poderá migrar esse software para as máquinas virtuais do Azure e usá-lo no estado em que se encontra.
