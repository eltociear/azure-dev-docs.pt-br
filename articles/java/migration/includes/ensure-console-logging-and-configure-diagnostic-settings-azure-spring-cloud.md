---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 3ff76d977c231b4b238f9dbec7f3bfaddfe5e484
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507588"
---
### <a name="ensure-console-logging-and-configure-diagnostic-settings"></a>Verificar o registro em log de console e definir as configurações de diagnóstico

Configure o log para que toda a saída seja roteada para o console e não para arquivos.

Depois da implantação de um aplicativo no Azure Spring Cloud, [adicione uma configuração de diagnóstico](/azure/spring-cloud/diagnostic-services) para disponibilizar os eventos registrados para o consumo, por exemplo, por meio do Log Analytics do Azure Monitor.

#### <a name="logstashelk-stack"></a>LogStash/ELK Stack

Se você usar o LogStash/ELK Stack para a agregação de log, defina a configuração de diagnóstico para transmitir a saída de console para um [hub de eventos do Azure](/azure/event-hubs/). Em seguida, use o [Plug-in do LogStash para Hub de Eventos](https://github.com/logstash-plugins/logstash-input-azure_event_hubs) a fim de ingerir eventos registrados no LogStash.

#### <a name="splunk"></a>Splunk

Se você usar o Splunk para a agregação de log, defina a configuração de diagnóstico para transmitir a saída de console para o [Armazenamento de Blobs do Azure](/azure/storage/blobs/). Em seguida, use o [Complemento do Splunk para os serviços em nuvem da Microsoft](https://splunkbase.splunk.com/app/3757/) para ingerir eventos registrados no Splunk.
