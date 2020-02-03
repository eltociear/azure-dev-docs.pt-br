---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: e36fae7adfda8c05e222151872b137fa600cdd97
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830764"
---
### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>Determinar se as Filas ou os Tópicos JMS estão em uso

Se seu aplicativo estiver usando Filas ou Tópicos JMS, você precisará migrá-los para um servidor JMS hospedado externamente. O Barramento de Serviço do Azure e o Advanced Message Queuing Protocol podem ser uma excelente estratégia de migração para pessoas que usam JMS. Para obter mais informações, consulte [Usar o Java Message Service (JMS) com o Barramento de Serviço do Azure e o AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

Se os armazenamentos persistentes JMS tiverem sido configurados, você deverá capturar a configuração deles e aplicá-la após a migração.

Se você estiver usando o Oracle Message Broker, poderá migrar esse software para as máquinas virtuais do Azure e usá-lo no estado em que se encontra.
