---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 64a4a0e191c70d930ad8c5f9d19cb81dfb9cb851
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673412"
---
### <a name="determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries"></a>Determinar se você está usando suas próprias bibliotecas Java EE compartilhadas personalizadas criadas

Se você estiver usando o recurso de biblioteca Java EE compartilhada, terá duas opções:

* Refatore o código do aplicativo para remover todas as dependências em suas bibliotecas e incorporar a funcionalidade diretamente ao seu aplicativo.
* Adicione as bibliotecas ao classpath do servidor.
