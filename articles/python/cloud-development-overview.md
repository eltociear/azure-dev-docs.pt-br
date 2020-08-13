---
title: Desenvolvimento em nuvem com o Azure - O que é Azure?
description: Uma visão geral do desenvolvimento de aplicativos em nuvem no Microsoft Azure, começando com o modo como os data centers, os serviços e os recursos se relacionam.
ms.date: 05/12/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: e776ce3aa84921dcfcb1be1e8613ae7332fe61a8
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983168"
---
# <a name="cloud-development-on-azure"></a>Desenvolvimento em nuvem com o Azure

Você é um desenvolvedor de Python e está pronto para desenvolver aplicativos de nuvem para Microsoft Azure. Para ajudá-lo a se preparar para uma carreira longa e produtiva, esta série de três artigos orienta você em relação ao panorama básico do desenvolvimento em nuvem no Azure.

## <a name="what-is-azure-data-centers-services-and-resources"></a>O que é o Azure? Data centers, serviços e recursos

O CEO da Microsoft, Satya Nadella, geralmente se refere ao Azure como "o computador do mundo". Um computador, como você sabe, é uma coleção de hardware gerenciada por um sistema operacional, que fornece uma plataforma na qual você pode criar software que ajuda as pessoas a aplicar o poder de computação do sistema a várias tarefas. (É por isso que usamos a palavra "aplicativo" para descrever esse software.)

No caso do Azure, o hardware do computador não é um único computador, mas um enorme pool de computadores de servidor virtualizados contidos em [dezenas de data centers em massa no mundo inteiro](https://azure.microsoft.com/global-infrastructure/regions/). O "sistema operacional" do Azure é composto de *serviços* que alocam e desalocam dinamicamente partes diferentes do pool de recursos conforme os aplicativos precisam delas. Cada alocação&mdash;seja a capacidade de computação (núcleos de CPU e memória), armazenamento, bancos de dados, redes e assim por diante&mdash;é chamada de  *recurso*. Cada recurso discreto é atribuído adequadamente a um identificador de objeto *exclusivo* (um GUID) e uma URL exclusiva.

![Camadas do Azure, do data center até os serviços do Azure para alocar recursos](media/cloud-development/azure-layers.png)

Os recursos são os blocos de construção de um aplicativo em nuvem. Assim, o processo de desenvolvimento em nuvem começa com a criação do ambiente apropriado no qual você pode implantar as diferentes partes do aplicativo. Resumindo, não é possível implantar nenhum código ou dados no Azure até que você tenha alocado e configurado, &mdash;ou seja, *provisionado*&mdash;um recurso de destino adequado, como uma máquina virtual, um banco de dados, uma conta de armazenamento, um registro de contêiner, um orquestrador de contêiner, um host da Web, uma rede virtual, os mecanismos de IA e de análise e assim por diante.

O processo de criação do ambiente para seu aplicativo, em seguida, envolve identificar os serviços relevantes e os tipos de recursos envolvidos e, em seguida, provisionar esses recursos (quando você começa a alugá-los do Azure). Na verdade, há centenas de diferentes tipos de recursos à sua disposição, de recursos básicos de "infraestrutura", como máquinas virtuais, em que você mantém o controle total e a responsabilidade pelo software implantado, até serviços de "plataforma" de nível superior, que fornecem um ambiente mais gerenciado no qual você se preocupa com apenas o código de dados e aplicativos.

Encontrar os serviços certos para seu aplicativo e equilibrar os custos relativos pode ser desafiador, mas também faz parte da diversão criativa do desenvolvimento em nuvem. Outros artigos sobre este Centro de desenvolvedores ajudam você a entender suas escolhas. Enquanto isso, vamos discutir como você realmente trabalha com todos esses serviços e recursos.

> [!NOTE]
> Você provavelmente já viu e talvez tenha crescido o desgaste dos termos "IaaS" (infraestrutura como serviço), "PaaS" (plataforma como serviço) e assim por diante... A parte "como um serviço" reflete a realidade de que você geralmente não tem acesso físico aos próprios data centers. Em vez disso, você usa ferramentas como o portal do Azure, a CLI do Azure ou a API REST do Azure para provisionar recursos de "infraestrutura", recursos de "plataforma" e assim por diante. Como um "serviço", o Azure está sempre pronto para receber suas solicitações.
>
> Neste centro de desenvolvedores, não abordaremos IaaS, PaaS, entre outros jargões porque "como um serviço" é apenas inerente à nuvem!

> [!NOTE]
> Uma "nuvem híbrida" refere-se à combinação de computadores e data centers privados com recursos de nuvem como o Azure e tem suas próprias considerações além do que é abordado na discussão anterior. Além disso, essa discussão pressupõe um novo desenvolvimento de aplicativos; os cenários que envolvem uma nova arquitetura e a migração de aplicativos locais existentes não são abordados aqui.

## <a name="next-step"></a>Próxima etapa

> [!div class="nextstepaction"]
> [Provisionamento, acesso e gerenciamento de recursos >>>](cloud-development-provisioning.md)
