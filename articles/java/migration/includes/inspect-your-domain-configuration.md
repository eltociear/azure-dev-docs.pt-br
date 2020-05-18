---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 750a8cdd34ead8390a0c4c2cb028f4624bd256a9
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988910"
---
### <a name="inspect-your-domain-configuration"></a>Inspecionar a configuração de domínio

A unidade de configuração principal no WebLogic Server é o domínio. Portanto, o arquivo *config.xml* contém uma infinidade de configurações que você precisa considerar cuidadosamente para a migração. O arquivo inclui referências a arquivos XML adicionais que são armazenados em subdiretórios. A Oracle recomenda que você use normalmente o **Console de Administração** para configurar os objetos e os serviços gerenciáveis do WebLogic Server e permitir que o WebLogic Server mantenha o arquivo *config.xml*. Para saber mais, consulte [Arquivos de configuração de domínio](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/domcf/config_files.html).

#### <a name="inside-your-application"></a>Dentro de seu aplicativo

Inspecione o arquivo *WEB-INF/weblogic.xml* e/ou o arquivo *WEB-INF/web.xml*.
