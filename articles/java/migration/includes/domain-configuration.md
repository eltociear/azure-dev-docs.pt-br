---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 33146c309d8fdaf21dc218c11054460cfc3dc8f4
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673582"
---
### <a name="domain-configuration"></a>Configuração de domínio

A unidade de configuração principal no WebLogic Server é o domínio. Portanto, o arquivo *config.xml* contém uma infinidade de configurações que você precisa considerar cuidadosamente para a migração. O arquivo inclui referências a arquivos XML adicionais que são armazenados em subdiretórios. A Oracle recomenda que você use normalmente o **Console de Administração** para configurar os objetos e os serviços gerenciáveis do WebLogic Server e permitir que o WebLogic Server mantenha o arquivo *config.xml*. Para saber mais, consulte [Arquivos de configuração de domínio](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/domcf/config_files.html).

#### <a name="inside-your-application"></a>Dentro de seu aplicativo

Inspecione o arquivo *WEB-INF/weblogic.xml* e/ou o arquivo *WEB-INF/web.xml*.
