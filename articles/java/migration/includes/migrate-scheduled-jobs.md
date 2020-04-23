---
author: yevster
ms.author: yebronsh
ms.date: 1/22/2020
ms.openlocfilehash: 1015c179c0f93485decd77bd89a3ceec8833652e
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81672152"
---
### <a name="migrate-scheduled-jobs"></a>Migrar os trabalhos agendados

Para realizar trabalhos agendados no Azure, considere a possibilidade de usar um [Gatilho de temporizador para Azure Functions](/azure/azure-functions/functions-bindings-timer). Você não precisa migrar o código do trabalho propriamente dito para uma função. A função pode simplesmente invocar uma URL no aplicativo para disparar o trabalho. Se essas execuções de trabalho precisarem ser invocadas dinamicamente e/ou rastreadas de forma centralizada, considere a possibilidade de usar um [lote do Spring](https://spring.io/projects/spring-batch).

Como alternativa, você pode criar um aplicativo lógico com um gatilho de recorrência para invocar a URL sem gravar nenhum código fora do aplicativo. Para obter mais informações, consulte [Visão geral: O que são os Aplicativos Lógicos do Azure?](/azure/logic-apps/logic-apps-overview) e [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de recorrência em Aplicativos Lógicos do Azure](/azure/connectors/connectors-native-recurrence).

> [!NOTE]
> Para evitar o uso mal-intencionado, você provavelmente precisará garantir que o ponto de extremidade de invocação do trabalho exija credenciais. Nesse caso, a função de gatilho precisará fornecer as credenciais.
