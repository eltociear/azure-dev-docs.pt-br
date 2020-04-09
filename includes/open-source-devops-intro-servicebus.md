---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80741024"
---
O [Barramento de Serviço do Azure](/azure/service-bus-messaging/service-bus-messaging-overview) é um agente de mensagens de [integração](https://azure.microsoft.com/product-categories/integration/) empresarial. O barramento de serviço é compatível com dois tipos de comunicação: filas e tópicos. 

As filas são compatíveis com comunicações assíncronas entre aplicativos. Um aplicativo envia mensagens a uma fila, que as armazena. O aplicativo de recebimento conecta-se às mensagens da fila e as lê.

Os tópicos dão suporte ao padrão publicar-assinar, que habilita uma relação um-para-muitos entre o originador da mensagem e o(s) receptor(es) da mensagem.