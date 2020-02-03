---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 33146c309d8fdaf21dc218c11054460cfc3dc8f4
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830974"
---
### <a name="domain-configuration"></a>Configuração de domínio

A unidade de configuração principal no WebLogic Server é o domínio. Portanto, o arquivo *config.xml* contém uma infinidade de configurações que você precisa considerar cuidadosamente para a migração. O arquivo inclui referências a arquivos XML adicionais que são armazenados em subdiretórios. A Oracle recomenda que você use normalmente o **Console de Administração** para configurar os objetos e os serviços gerenciáveis do WebLogic Server e permitir que o WebLogic Server mantenha o arquivo *config.xml*. Para saber mais, consulte [Arquivos de configuração de domínio](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/domcf/config_files.html).

#### <a name="inside-your-application"></a>Dentro de seu aplicativo

Inspecione o arquivo *WEB-INF/weblogic.xml* e/ou o arquivo *WEB-INF/web.xml*.
