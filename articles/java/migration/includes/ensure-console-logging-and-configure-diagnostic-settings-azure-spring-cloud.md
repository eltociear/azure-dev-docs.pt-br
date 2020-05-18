---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: b53308d4a9db52a25665d0daa74be678e726c499
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990178"
---
### <a name="ensure-console-logging-and-configure-diagnostic-settings"></a>Verificar o registro em log de console e definir as configurações de diagnóstico

Configure o registro em log para que todos os aplicativos no Azure Spring Cloud registrem no console e não em arquivos.

Depois da implantação de um aplicativo no Azure Spring Cloud, [adicione uma configuração de diagnóstico](/azure/spring-cloud/diagnostic-services) para disponibilizar os eventos registrados para o consumo, por exemplo, por meio do Log Analytics do Azure Monitor.

#### <a name="logstashelk-stack"></a>LogStash/ELK Stack

Se você usar o LogStash/ELK Stack para a agregação de log, defina a configuração de diagnóstico para transmitir a saída de console para um [hub de eventos do Azure](/azure/event-hubs/). Em seguida, use o [Plug-in do LogStash para Hub de Eventos](https://github.com/logstash-plugins/logstash-input-azure_event_hubs) a fim de ingerir eventos registrados no LogStash.

#### <a name="splunk"></a>Splunk

Se você usar o Splunk para a agregação de log, defina a configuração de diagnóstico para transmitir a saída de console para o [Armazenamento de Blobs do Azure](/azure/storage/blobs/). Em seguida, use o [Complemento do Splunk para os serviços em nuvem da Microsoft](https://splunkbase.splunk.com/app/3757/) para ingerir eventos registrados no Splunk.
