---
title: Provisionamento, acesso e gerenciamento de recursos no Azure
description: Uma visão geral dos métodos usados para trabalhar com recursos do Azure, incluindo o portal do Microsoft Azure, a CLI do Azure e as bibliotecas do Azure (SDK).
ms.date: 05/27/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 457970eb359be9f10c6269e1ea98efec76612009
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983208"
---
# <a name="provisioning-accessing-and-managing-resources-on-azure"></a>Provisionamento, acesso e gerenciamento de recursos no Azure

[Artigo anterior: visão geral](cloud-development-overview.md)

Conforme descrito no artigo anterior desta série, uma parte essencial do desenvolvimento de um aplicativo em nuvem é o provisionamento dos recursos necessários no Azure para o qual você pode implantar seu código e dados.

Como esse provisionamento é feito, exatamente? Como pedir ao Azure para alocar recursos para seu aplicativo e como você configura e acessa esses recursos? Resumindo, como você se comunica com o próprio Azure para colocar todos esses recursos em funcionamento?

## <a name="means-of-communicating-with-azure"></a>Meios de comunicação com o Azure

A resposta é simples. Assim como na maioria dos sistemas operacionais, você pode se comunicar com o Azure por meio de três rotas: interface do usuário, interface de linha de comando e API.

![Os diferentes meios de comunicação com o Azure para provisionar recursos](media/cloud-development/communication-with-azure.png)

Você pode usar qualquer um ou todos esses métodos complementares para criar, configurar e gerenciar quaisquer recursos do Azure necessários. Na verdade, normalmente você usa todos os três no decorrer de um projeto de desenvolvimento, e vale a pena se familiarizar com cada um deles.

Neste centro do desenvolvedor, mostramos principalmente o uso da CLI e do código Python que usam as bibliotecas do Azure, pois o uso do portal é bem abordado na documentação de cada serviço individual.

> [!NOTE]
> As bibliotecas do Azure para Python são, às vezes, chamadas de SDK do Azure para Python. No entanto, não há nenhum componente do SDK além das bibliotecas, que você adquire por meio do Gerenciador de pacotes do Python, pip.

## <a name="azure-portal"></a>Portal do Azure

O [portal do Azure](https://portal.azure.com) é a interface do usuário totalmente personalizável e baseada em navegador do Azure por meio da qual você pode provisionar e gerenciar recursos com todos os serviços do Azure. Para acessar o portal, primeiro você deve entrar usando uma conta da Microsoft e, em seguida, criar uma conta gratuita do Azure com uma assinatura. (Depois de conectado, você pode selecionar o ícone **?** e selecionar **Iniciar o tour guiado** para obter uma explicação simples dos recursos do portal principal.)

**Prós**: a interface do usuário facilita a exploração de serviços e de todas as várias opções de configuração. A configuração de valores de configuração é segura porque nenhuma informação é armazenada na estação de trabalho local.

**Contras**: Trabalhar com o portal é um processo manual e não pode ser automatizado. Lembrar o que você fez para alterar uma configuração, por exemplo, significa registrar suas etapas em um documento separado.

## <a name="azure-cli"></a>CLI do Azure

A [CLI do Azure](/cli/azure/?view=azure-cli-latest) é a interface de linha de comando de [software livre](https://github.com/Azure/azure-cli) do Azure. Quando você estiver conectado à CLI (usando o comando `az login`), poderá executar as mesmas tarefas que você faz por meio do portal.
  
**Prós**: Facilmente automatizado por meio de scripts e processamento de saída. Fornece comandos de nível superior que provisionam vários recursos em conjunto para tarefas comuns, como a implantação de um aplicativo Web. Os scripts podem ser gerenciados no controle do código-fonte.

**Contras**: Curva de aprendizado mais acentuada do que usar o portal, e os comandos estão sujeitos a bugs. Mensagens de erro não são sempre úteis.

Você também pode usar o [Azure PowerShell](/powershell/) no lugar da CLI do Azure, embora os comandos do estilo Linux da CLI do Azure sejam geralmente mais conhecidos para desenvolvedores do Python.

No lugar da CLI local ou do PowerShell, você pode usar o Azure Cloud Shell diretamente por meio de [https://shell.azure.com/](https://shell.azure.com/). O Cloud Shell é conveniente porque é autenticado automaticamente com o Azure quando ele é aberto e tem as mesmas funcionalidades que você faria por meio do portal do Azure. No entanto, como o Cloud Shell não é um ambiente local, ele é mais adequado para operações singulares como você faria por meio do portal em vez da automação com script.

## <a name="azure-rest-api-and-azure-libraries"></a>API REST do Azure e bibliotecas do Azure

A [API REST do Azure](/rest/api/?view=Azure) é a interface programática do Azure, fornecida por meio do REST seguro por HTTP, porque os data centers do Azure estão todos conectados à Internet. Cada recurso recebe uma URL exclusiva que permite uma API específica do recurso, sujeita a rigorosos protocolos de autenticação e políticas de acesso. (O portal do Azure e a CLI do Azure, na verdade, fazem seu trabalho por meio da API REST.)

Para os desenvolvedores, as bibliotecas do Azure fornecem bibliotecas específicas a um idioma que movem as funcionalidades da API REST em paradigmas de programação muito mais convenientes, como classes e objetos. Para Python, instale sempre as bibliotecas individuais com `pip install` em vez de instalar um SDK independente como um todo. (Para outros idiomas, consulte [downloads do SDK do Azure](https://azure.microsoft.com/downloads/).)

**Prós**: Controle preciso sobre todas as operações, incluindo um meio muito mais direto de usar a saída de uma operação como entrada para outra. Para desenvolvedores de Python, isso permite trabalhar em paradigmas de linguagem familiares em vez de usar a CLI. Também pode ser usado com o código do aplicativo para automatizar cenários de gerenciamento.
  
**Contras**: As operações que podem ser feitas com um comando da CLI normalmente exigem várias linhas de código, todas sujeitas a bugs. Não fornece operações de nível superior, como a CLI do Azure.

## <a name="automatic-on-demand-provisioning"></a>Provisionamento automático sob demanda

Muitos serviços do Azure permitem que você configure características de dimensionamento para atender à demanda variável. Nesse caso, o Azure pode provisionar automaticamente recursos adicionais e desalocá-los conforme apropriado. Esse dimensionamento automático é uma das principais vantagens de uma plataforma de nuvem apoiada pelos recursos de vários data centers. Em vez de projetar seu ambiente para picos de demanda, pagando pela capacidade que normalmente não estaria utilizando, você pode projetar o ambiente para a linha de base ou o uso médio e pagar pelo recurso adicional somente quando necessário.

Para obter mais informações sobre o [dimensionamento automático](/azure/architecture/best-practices/auto-scaling), confira no Centro de Arquitetura do Azure.

## <a name="subscriptions-resource-groups-and-regions"></a>Assinaturas, grupos de recursos e regiões

No modelo de recursos do Azure, você pode imaginar que, ao longo do tempo, estará provisionando muitos recursos diferentes em vários serviços do Azure para diferentes aplicativos. Há três níveis de hierarquia que você pode usar para organizar esses recursos:

1. **Assinaturas**: cada assinatura do Azure tem a própria conta de cobrança e muitas vezes representa uma equipe ou um departamento distinto dentro de uma organização. Em geral, você provisiona todos os recursos necessários para qualquer aplicativo dentro da mesma assinatura, para que eles possam se beneficiar de recursos como autenticação compartilhada. No entanto, como todos os recursos podem ser acessados por meio de URLs públicas e dos tokens de autorização necessários, é certamente possível distribuir recursos entre várias assinaturas.

1. **Grupos de recursos**: dentro de uma assinatura, os grupos de recursos são contêineres para outros recursos, que você pode gerenciar como um grupo. (Por esse motivo, um grupo de recursos normalmente está relacionado a um projeto específico.) Sempre que você provisiona um recurso, na verdade, deve especificar o grupo ao qual ele pertence. Sua primeira etapa com um novo projeto costuma criar um grupo de recursos apropriado. Ao excluir o grupo de recursos, você desaloca todos os recursos contidos, em vez de ter que excluir cada recurso individualmente. Confie em nós quando afirmamos que negligenciar a organização dos seus grupos de recursos pode levar a muitas dores de cabeça posteriormente, quando você não se lembra de qual recurso pertence a qual projeto!

1. **Nomenclatura de recursos**: em um grupo de recursos, você pode usar quaisquer estratégias de nomenclatura que desejar para expressar semelhanças ou relações entre os recursos. Como o nome é geralmente usado na URL do recurso, pode haver limitações nos caracteres. (Alguns nomes, por exemplo, permitem apenas letras e números, enquanto outros permitem hifens e sublinhados.)

Ao trabalhar com o Azure, você desenvolverá suas preferências para organizar seus recursos e suas convenções de nomenclatura de assinaturas, grupos de recursos e recursos individuais.

### <a name="regions-and-geographies"></a>Regiões e geografias

Uma característica importante de um grupo de recursos é que ele está sempre associado a uma *região* específica do Azure, que é o local do data center específico. Todos os recursos no mesmo grupo estão colocalizados nesse data center e, portanto, podem interagir com muito mais eficiência do que se estivessem em regiões diferentes. Os desenvolvedores geralmente escolhem regiões mais próximas de seus clientes, otimizando, assim, a capacidade de resposta de um aplicativo. O Azure também oferece recursos de replicação geográfica para sincronizar cópias de seu aplicativo e bancos de dados em várias regiões e permitir que você atenda melhor a uma base de clientes global.

Devido a leis e regulamentos locais, que são determinados pela *geografia* na qual você cria uma assinatura, você pode ter acesso a apenas determinadas regiões, e essas regiões talvez não permitam todos os serviços do Azure. Para obter detalhes, consulte [Infraestrutura global do Azure](https://azure.microsoft.com/global-infrastructure/).

## <a name="next-step"></a>Próxima etapa

> [!div class="nextstepaction"]
> [Fluxo de desenvolvimento do Azure >>>](cloud-development-flow.md)
