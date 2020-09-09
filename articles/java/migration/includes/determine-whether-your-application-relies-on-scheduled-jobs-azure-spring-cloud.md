---
author: yevster
ms.author: yebronsh
ms.date: 7/21/2020
ms.openlocfilehash: b1acff280bddea700b0382609c1003129b05dad2
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062635"
---
#### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>Determinar se o aplicativo depende de trabalhos agendados

Trabalhos agendados, como tarefas do Agendador do Quartz ou trabalhos cron do Unix, NÃO devem ser usados com o Azure Spring Cloud. O Azure Spring Cloud não impedirá que você implante um aplicativo que contenha tarefas agendadas internamente. No entanto, se o aplicativo for escalado horizontalmente, um mesmo trabalho agendado poderá ser executado mais de uma vez por período agendado. Essa situação pode levar a consequências indesejadas.

Inventarie todas as tarefas agendadas em execução nos servidores de produção, dentro ou fora do código do aplicativo.
