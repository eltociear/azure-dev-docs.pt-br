---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 2dccfba5cfe61ac862aeabe7b928e121502093b7
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830874"
---
### <a name="determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries"></a>Determinar se você está usando suas próprias bibliotecas Java EE compartilhadas personalizadas criadas

Se você estiver usando o recurso de biblioteca Java EE compartilhada, terá duas opções:

1. Refatore o código do aplicativo para remover todas as dependências em suas bibliotecas e incorporar a funcionalidade diretamente ao seu aplicativo.
2. Adicione as bibliotecas ao classpath do servidor.
