---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 4d2df28d5c752d20454c28a6d4a53698aa7ff5b6
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673012"
---
### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>Determinar se o aplicativo depende de trabalhos agendados

Trabalhos agendados, como tarefas do Agendador do Quartz ou trabalhos cron do Unix, NÃO podem ser usados com o Serviço de Kubernetes do Azure. O Serviço de Kubernetes do Azure não impedirá que você implante um aplicativo que contém tarefas agendadas internamente. No entanto, se o aplicativo for escalado horizontalmente, um mesmo trabalho agendado poderá ser executado mais de uma vez por período agendado. Essa situação pode levar a consequências indesejadas.

Para executar trabalhos agendados em seu cluster do AKS, defina CronJobs do Kubernetes conforme necessário. Para obter mais informações, confira [Execução de tarefas automatizadas com um CronJob](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/).
