---
title: Como registrar em log com o SDK do Azure para JavaScript
description: Saiba como habilitar o registro em log com o SDK do Azure para bibliotecas de clientes do JavaScript
ms.topic: article
ms.date: 07/23/2020
ms.author: dsindona
author: dsindona
ms.openlocfilehash: 1fd961709723891824cedfd0d9b853a328cb2e46
ms.sourcegitcommit: 8cd0ddf1651c3b64bb72dedc2890108c2cfe3bcb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87335232"
---
# <a name="logging-with-the-azure-sdk-for-javascript"></a>Como registrar em log com o SDK do Azure para JavaScript

O SDK do Azure para a biblioteca principal do JavaScript inclui uma biblioteca de agente que permite que você habilite facilmente o registro em log para seu aplicativo. 

[Pacote NPM](https://www.npmjs.com/package/@azure/logger) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)

## <a name="enable-logging"></a>Habilitar o registro em log

Você pode habilitar o registro em log em todo o aplicativo usando uma única variável de ambiente ou pode configurar dinamicamente o registro em log para uma parte do seu aplicativo. Este artigo explica os principais conceitos sobre o pacote do agente e como habilitar o registro em log com os seguintes métodos:

- Defina a variável de ambiente `AZURE_LOG_LEVEL`.
- Chame o `setLogLevel` importado da biblioteca do agente.
- Chame `enable()` em agentes específicos.

> [!NOTE]
> Este artigo se aplica às bibliotecas de clientes que usam as versões mais recentes do SDK do Azure. Para ver se uma biblioteca é compatível, veja a lista de [versões mais recentes do SDK do Azure](https://azure.github.io/azure-sdk/releases/latest/index.html#javascript). Se o aplicativo estiver usando uma versão mais antiga das bibliotecas de clientes do SDK do Azure, veja as instruções específicas na documentação do serviço aplicável.

## <a name="log-levels"></a>Níveis de log

O pacote `@azure/logger` dá suporte aos seguintes níveis de log especificados na ordem de mais detalhado para menos detalhado:

- verbose
- informações
- warning
- error

Quando você define um nível de log, seja de modo programático ou por meio da variável de ambiente `AZURE_LOG_LEVEL`, todos os logs gravados com um nível de log igual ou menor que o que você escolher serão emitidos. Por exemplo, se você definir o nível de log como `warning`, todos os logs com o nível `warning` ou `error` serão emitidos.

## <a name="install-the-logger-package"></a>Instalar o pacote do agente

A biblioteca do agente do SDK do Azure para JavaScript é entregue como um pacote [npm](https://www.npmjs.com/). Use o npm para instalar o pacote `@azure/logger`:

```cmd
npm install @azure/logger
```

## <a name="set-the-logging-environment-variable"></a>Defina a variável de ambiente de registro em log

Você pode usar a variável de ambiente `AZURE_LOG_LEVEL` única para habilitar o registro em log em seu aplicativo. Os logs serão gerados para stderr. Depois de definir a variável de ambiente, você precisará reiniciar o aplicativo para iniciar a geração de logs.

Este exemplo de bash define o nível de log como detalhado:

```bash
export AZURE_LOG_LEVEL="verbose"
```

Este exemplo usa o PowerShell:

```powershell
$env:AZURE_LOG_LEVEL="verbose"
```

Este exemplo usa o CMD:

```cmd
set AZURE_LOG_LEVEL="verbose"
```

## <a name="configure-dynamic-logging"></a>Configurar registrar em log dinâmico

O SDK do Azure para JavaScript permite que você habilite dinamicamente o registro em log conforme necessário ou para bibliotecas de clientes específicas. Isso acomoda os desenvolvedores que desejam usar uma implementação de registro em log personalizada para alguns componentes de aplicativo ou que desejam logs temporários para depuração.

Você pode usar o módulo `@azure/logger` para definir o nível de log no código:

```js
import { setLogLevel } from "@azure/logger";

setLogLevel("verbose");
```

Para habilitar um canal de log específico, importe `logger` do pacote para o qual você deseja emitir logs. O seguinte exemplo habilita o registro em log de informações somente para Hubs de Eventos:

```js
import { logger } from "@azure/event-hubs";

logger.info.enable();
```

## <a name="redirect-log-output"></a>Redirecionar saída de log

Para tratar mensagens de log por conta própria, reatribua o método `log` no `AzureLogger` ou qualquer agente importado de uma biblioteca de clientes.

Este exemplo redireciona as mensagens de log para stderr:

```js
import { AzureLogger } from "@azure/logger";

AzureLogger.log = msg => console.error(msg);
```

Este exemplo redireciona somente mensagens de log de informações do EventHub do Azure:

```js
import { logger } from "@azure/event-hubs";
logger.info.log = msg => console.error(msg);
```

## <a name="next-steps"></a>Próximas etapas

- [Habilitar log de diagnósticos para aplicativos no Serviço de Aplicativo do Azure](/azure/app-service/troubleshoot-diagnostic-logs)
- [Examinar o log de segurança do Azure e as opções de auditoria](/azure/security/fundamentals/log-audit)
- [Saiba como trabalhar com os logs da plataforma Azure](/azure/azure-monitor/platform/platform-logs-overview)