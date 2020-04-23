---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 9403614c7ae2990a35fcbcce6d2e104f87724da5
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673522"
---
### <a name="determine-the-network-topology"></a>Determinar a topologia de rede

O conjunto atual de ofertas do Marketplace é um ponto de partida para sua migração. Se a oferta não abranger aspectos de sua arquitetura que você precisa migrar, será necessário capturar a topologia de rede de sua implantação existente e reproduzi-la no Azure, mesmo depois de terminar a oferta básica com um dos modelos de solução.

Este é um tópico muito amplo, mas as referências a seguir podem dar uma direção aos seus esforços de migração:

* Esta referência enumera os tópicos de alto nível relevantes para a migração da topologia de rede para o Azure: [Guia de implantação do Fast Track](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/deploying.html#GUID-E0BE4A3E-44CD-4C95-9540-7A850BF02F6A).
* Esta referência descreve as preocupações importantes com relação ao clustering, que tem um impacto na topologia de rede: [Clustering do WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/clustering.html#GUID-E39A18C2-B990-485F-BFB1-0549250FABFE).
* Como as fontes de dados são servidores separados em um sistema WebLogic, você deve considerá-las como parte da análise de topologia de rede. [Fontes de dados do WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/jdbc.html#GUID-9FD5F552-B2E4-4FEC-8C10-503A08764B52).
* As fontes de mensagens também são servidores separados. [Mensagens do WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/jms.html#GUID-3B5F647D-E001-413B-AC6A-1E103BDBA93F)
* O balanceamento de carga é um requisito fundamental. Esta referência cobre o lado do WebLogic Server de balanceamento de carga: [Balanceamento de carga em um cluster](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/clust/load_balancing.html#GUID-B8F6DE4B-1AAC-428B-878B-BFDCE161C054).
