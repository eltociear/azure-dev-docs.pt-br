---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: e4370e6db3589e6050395fe806541505fa3eb8bb
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738106"
---
### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>Determinar se o aplicativo depende de trabalhos agendados

Trabalhos agendados, como tarefas do Agendador do Quartz ou trabalhos cron do Unix, NÃO podem ser usados com o Serviço de Kubernetes do Azure. O Serviço de Kubernetes do Azure não impedirá que você implante um aplicativo que contém tarefas agendadas internamente. No entanto, se o aplicativo for escalado horizontalmente, um mesmo trabalho agendado poderá ser executado mais de uma vez por período agendado. Essa situação pode levar a consequências indesejadas.

Para executar trabalhos agendados em seu cluster do AKS, defina CronJobs do Kubernetes conforme necessário. Para obter mais informações, confira [Execução de tarefas automatizadas com um CronJob](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/).
