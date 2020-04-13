---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: a521396292214fd4e68b4625a0e5f5bcfca8be92
ms.sourcegitcommit: 951fc116a9519577b5d35b6fb584abee6ae72b0f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80612124"
---
### <a name="determine-whether-java-message-service-jms-queues-or-topics-are-in-use"></a>Determinar se as Filas ou os Tópicos do JMS (Java Message Service) estão em uso

Se seu aplicativo estiver usando Filas ou Tópicos do JMS, você precisará migrá-los para um servidor do JMS hospedado externamente. O Barramento de Serviço do Azure e o AMQP (Advanced Message Queuing Protocol) podem ser uma excelente estratégia de migração para pessoas que usam o JMS. Para obter mais informações, consulte [Usar o JMS com o Barramento de Serviço do Azure e o AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

Se os armazenamentos persistentes JMS tiverem sido configurados, você deverá capturar a configuração deles e aplicá-la após a migração.
