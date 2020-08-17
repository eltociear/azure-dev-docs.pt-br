---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: f50115be65be8e746527b3d4ee833a738109ddbc
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88052243"
---
### <a name="determine-whether-weblogic-clustering-is-used"></a>Determinar se o clustering do WebLogic é usado

É provável que você tenha implantado seu aplicativo em vários WebLogic Servers para conseguir alta disponibilidade. Você pode migrar esses clusters diretamente da instalação local para o WebLogic em execução em Máquinas Virtuais do Azure. Para obter mais informações, consulte [Arquivos de configuração de domínio](https://docs.oracle.com/middleware/12213/wls/DOMCF/config_files.htm#DOMCF127) na documentação da Oracle.

### <a name="account-for-load-balancing-requirements"></a>Conta para requisitos de balanceamento de carga

O balanceamento de carga é uma parte essencial da migração do Cluster do Oracle WebLogic Server para o Azure.  A solução mais fácil é usar o suporte interno para o [Gateway de Aplicativo do Azure](/azure/application-gateway/overview) fornecido na oferta do Azure Marketplace para o Cluster do Oracle WebLogic Server.  Para obter um tutorial sobre esse tópico, confira [Tutorial: Migrar um cluster do WebLogic Server para o Azure com o Gateway de Aplicativo do Azure como um balanceador de carga](../migrate-weblogic-with-app-gateway.md).

Para obter um resumo das funcionalidades do Gateway de Aplicativo do Azure em comparação com outras soluções de balanceamento de carga do Azure, confira [Visão geral das opções de balanceamento de carga no Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).
