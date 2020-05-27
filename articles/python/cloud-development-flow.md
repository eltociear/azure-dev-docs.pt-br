---
title: Fluxo de desenvolvimento do Azure
description: Uma visão geral do ciclo de desenvolvimento em nuvem no Azure, que envolve provisionamento, codificação, teste, implantação e gerenciamento.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 577b813e2b4ccd8d2cae3d7999d9bb959f88adc2
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83404959"
---
# <a name="the-azure-development-flow-provision-code-test-deploy-and-manage"></a>O fluxo de desenvolvimento do Azure: provisionar, codificar, testar, implantar e gerenciar

[Artigo anterior: provisionamento, acesso e gerenciamento de recursos](cloud-development-provisioning.md)

Agora que você compreende o modelo de serviços e recursos do Azure, é possível entender o fluxo geral do desenvolvimento de aplicativos em nuvem com o Azure: **provisionar**, **codificar**, **testar**, **implantar** e **gerenciar**.

## <a name="step-1-provision-and-configure-resources"></a>Etapa 1: Provisionar e configurar recursos

Conforme descrito no [artigo anterior desta série](cloud-development-provisioning.md), a primeira etapa no desenvolvimento de qualquer aplicativo é provisionar e configurar os recursos que compõem o ambiente de destino do seu aplicativo.

O provisionamento começa com a criação de um grupo de recursos em uma região adequada do Azure. Você pode criar um grupo de recursos por meio do portal do Azure, por meio da CLI do Azure, ou com um script personalizado que usa o SDK do Azure (ou a API REST).

Nesse grupo de recursos, você provisiona e configura os recursos individuais necessários, usando novamente o portal, a CLI ou o SDK do Azure. A configuração inclui a definição de políticas de acesso que controlam quais identidades (entidades de serviço e/ou IDs de aplicativo) podem acessar esses recursos.

Para a maioria dos cenários de desenvolvimento, você provavelmente criará scripts de provisionamento com o código CLI do Azure e/ou Python usando o SDK do Azure. Esses scripts descrevem todas as necessidades de recursos do seu aplicativo e permitem recriá-los facilmente em ambientes diferentes de desenvolvimento, teste e produção (em oposição à execução manual de muitas etapas repetidas no portal do Azure). Esses scripts ASO facilitam o provisionamento de um ambiente em uma região diferente ou a utilização de grupos de recursos diferentes. Você também pode manter esses scripts em repositórios de controle do código-fonte para que tenha total histórico de alterações e auditoria.

## <a name="step-2-write-your-app-code-to-use-resources"></a>Etapa 2: Escreva o código do aplicativo para usar recursos

Depois de provisionar os recursos necessários para seu aplicativo, escreva o código do aplicativo que funciona com esses recursos (exceto os recursos nos quais você implanta o próprio código).

Por exemplo, na etapa de provisionamento, você pode ter criado uma conta de armazenamento do Azure e um contêiner de blob dentro dessa conta, além de ter definido políticas de acesso para o aplicativo nesse contêiner. No seu código, agora, você pode autenticar com essa conta de armazenamento e, em seguida, criar, atualizar ou excluir blobs dentro desse contêiner. (Esse processo é demonstrado no [Exemplo - Usar o armazenamento do Azure](azure-sdk-example-storage.md)) Da mesma forma, você pode ter provisionado um banco de dados com um esquema e permissões apropriadas, para que o código do aplicativo possa se conectar ao banco de dados e executar as operações usuais de criar-ler-atualizar-excluir.

Como desenvolvedor de Python, você normalmente vai escrever o código do aplicativo em Python usando o SDK do Azure para Python. Dito isso, qualquer parte independente de um aplicativo em nuvem pode ser escrita em qualquer idioma com suporte. Se você estiver trabalhando em uma equipe com uma variedade de conhecimentos em linguagens, por exemplo, é totalmente possível que algumas partes do aplicativo sejam escritas em Python, algumas em JavaScript, algumas em Java e outras em C#.

Observe que o código do aplicativo pode usar o SDK do Azure para executar operações de provisionamento e gerenciamento, conforme necessário. O provisionamento de scripts, da mesma forma, pode usar o SDK para inicializar recursos com dados específicos ou executar tarefas de manutenção em recursos de nuvem, mesmo quando esses scripts são executados localmente.

## <a name="step-3-test-and-debug-your-app-code-locally"></a>Etapa 3: Testar e depurar o código do aplicativo localmente

Os desenvolvedores normalmente gostam de testar o código do aplicativo em suas estações de trabalho locais, antes de implantá-lo na nuvem. Testar o código do aplicativo localmente significa que, em geral, você está acessando outros recursos que já provisionou na nuvem, como armazenamento, bancos de dados e assim por diante. A diferença é que você ainda não está executando o próprio código do aplicativo em um serviço de nuvem.

Ao executar o código localmente, você também pode aproveitar ao máximo os recursos de depuração oferecidos por ferramentas, como Visual Studio Code, e gerenciar seu código em um repositório de controle de origem.

Você não precisa modificar seu código para teste local: O Azure dá suporte total ao desenvolvimento local e à depuração usando o mesmo código implantado na nuvem. As variáveis de ambiente são fundamentais: na nuvem, seu código pode acessar as configurações do recurso de hospedagem como variáveis de ambiente. Ao criar essas mesmas variáveis de ambiente localmente, o mesmo código é executado sem modificação. Esse padrão funciona para credenciais de autenticação, URLs de recurso, cadeias de caracteres de conexão e qualquer número de outras configurações, facilitando o uso de recursos em um ambiente de desenvolvimento ao executar código localmente e recursos de produção depois que o código é implantado na nuvem.

## <a name="step-4-deploy-your-app-code-to-azure"></a>Etapa 4: Implantar seu código de aplicativo no Azure

Depois de testar seu código localmente, você estará pronto para implantar o código no recurso do Azure provisionado para hospedá-lo. Por exemplo, se você estiver escrevendo um aplicativo Web Django, implante esse código em uma máquina virtual (em que você fornece seu próprio servidor Web) ou para o Serviço de Aplicativo do Azure (que fornece o servidor Web para você). Depois de implantado, esse código é executado no servidor em vez de em seu computador local e pode acessar todos os recursos do Azure para os quais ele está autorizado.

Conforme observado na seção anterior, em processos de desenvolvimento típicos, você primeiro implanta seu código para os recursos que você provisionou em um ambiente de desenvolvimento. Após uma rodada de testes, você implanta seu código para os recursos em um ambiente de preparo, disponibilizando o aplicativo para sua equipe de teste e talvez para visualização prévia de clientes. Quando estiver satisfeito com o desempenho do aplicativo, você poderá implantar o código em seu ambiente de produção. Todas essas implantações também podem ser automatizadas por meio de integração contínua e implantação contínua usando o Azure DevOps.

No entanto, quando o código é implantado na nuvem, ele realmente se torna um aplicativo de nuvem, executado inteiramente nos computadores de servidor nos data centers do Azure.

## <a name="step-5-manage-monitor-and-revise"></a>Etapa 5: Gerenciar, monitorar e revisar

Após a implantação, você deseja ter certeza de que o aplicativo está sendo executado como deveria, respondendo às solicitações de clientes e usando recursos com eficiência (e com o menor custo). Você pode gerenciar como o Azure dimensiona automaticamente sua implantação conforme necessário e pode coletar e monitorar dados de desempenho por meio do portal do Azure, da CLI do Azure ou de scripts personalizados escritos com o SDK do Azure. Em seguida, você pode fazer ajustes em tempo real aos recursos provisionados para otimizar o desempenho, usando novamente qualquer uma das mesmas ferramentas.

O monitoramento fornece informações sobre como você pode reestruturar seu aplicativo de nuvem. Por exemplo, você pode descobrir que determinadas partes de um aplicativo Web (como um grupo de pontos de extremidade de API) são usadas apenas ocasionalmente em comparação com as partes principais. Em seguida, você pode optar por implantar essas APIs separadamente como Azure Functions sem servidor, onde elas têm seus próprios recursos de computação de backup que não competem com o aplicativo principal, mas que custam apenas alguns centavos por mês. Seu aplicativo principal torna-se mais responsivo para mais clientes, sem precisar escalar verticalmente para uma camada de custo mais alto.

## <a name="next-steps"></a>Próximas etapas

Agora você está familiarizado com a estrutura básica do Azure e o fluxo de desenvolvimento geral: provisionar recursos, escrever e testar código, implantar o código no Azure e, em seguida, monitorar e gerenciar esses recursos.

A próxima etapa é colocar a estação de trabalho totalmente configurada para funcionar com esse fluxo, em seguida, você está pronto para utilizar o SDK do Azure!

> [!div class="nextstepaction"]
> [Configurar seu ambiente de desenvolvimento local >>>](configure-local-development-environment.md)
