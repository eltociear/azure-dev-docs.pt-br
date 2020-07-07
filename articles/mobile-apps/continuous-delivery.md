---
title: Automatize a implantação e a versão dos seus aplicativos móveis com o Visual Studio App Center e os serviços do Azure
description: Saiba mais sobre os serviços como o App Center que ajudam a configurar o pipeline de entrega contínua para seus aplicativos móveis.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 96ca72ffb239b7baa48c06f09afe08a2653e3e0b
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632368"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatizar a implantação e o lançamento de seus aplicativos móveis com serviços de entrega contínua

Como desenvolvedores, vocês escrevem o código e fazem check-in dele no repositório de código, mas talvez os commits do check-in no repositório nem sempre sejam consistentes. Quando vários desenvolvedores trabalham no mesmo projeto, podem surgir problemas com a integração. As equipes podem se deparar com situações em que as coisas não funcionam, os bugs se acumulam e o desenvolvimento do projeto fica atrasado. Os desenvolvedores precisam esperar até que todo o código de software seja compilado e testado para verificar se há erros, o que torna o processo lento e menos iterativo.

Com a entrega contínua, você automatiza a implantação e o lançamento dos seus aplicativos móveis. Não importa se você está distribuindo o aplicativo para um grupo de testadores ou os funcionários da empresa (para teste beta) ou para uma loja de aplicativos (para produção). A entrega contínua torna as implantações menos arriscadas e incentiva as iterações rápidas. Você também pode lançar novas alterações para os clientes de maneira contínua.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuir binários de aplicativo para testadores beta

O teste beta do aplicativo móvel é uma das etapas críticas durante o processo de desenvolvimento do aplicativo. Ele ajuda a encontrar bugs e problemas no aplicativo logo no início. Os comentários aprimoram a qualidade do aplicativo quando você o está preparando para uso em produção.

Use os serviços a seguir para habilitar um pipeline de entrega contínua nos seus aplicativos móveis.

### <a name="visual-studio-app-center-distribute"></a>Visual Studio App Center Distribute

O [App Center Distribute](/appcenter/distribution/) é uma ferramenta para que os desenvolvedores lancem rapidamente builds para dispositivos. Com uma experiência completa de portal de instalação, o App Center Distribute é uma solução avançada para distribuição de testador de aplicativos beta. Também é uma alternativa conveniente para a distribuição por meio de lojas de aplicativos públicos. Os desenvolvedores podem automatizar ainda mais o fluxo de trabalho de distribuição com o App Center Build e as integrações às lojas de aplicativos públicos.

#### <a name="visual-studio-app-center-distribute-features"></a>Recursos do Visual Studio App Center Distribute

- Distribua seu aplicativo para usuários e testadores beta e verifique se todos os testadores estão usando a última versão do seu aplicativo.
- Notifique os testadores de novas versões sem que os testadores passem pelo fluxo de download novamente.
- Gerencie grupos de distribuição para versões diferentes do aplicativo.
- Distribua-o para lojas: 
  - [Apple](/appcenter/distribution/stores/apple)
  - [Google Play](/appcenter/distribution/stores/googleplay)
  - [Intune](/appcenter/distribution/stores/intune)
- Obtenha suporte de plataforma para iOS, Android, macOS, tvOS, Xamarin, React Native, Unity e Cordova.
- Registre automaticamente os dispositivos iOS no seu perfil de provisão.

#### <a name="visual-studio-app-center-distribute-references"></a>Referências do Visual Studio App Center Distribute

- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup)
- [Introdução ao App Center Distribute](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

O [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) é um serviço completo de CI (integração contínua) e de CD (entrega contínua) que funciona com o seu provedor Git preferido. O Azure Pipelines pode ser implantado na maioria dos principais serviços de nuvem, como os serviços do Azure. Comece a trabalhar com o seu código no GitHub, no Servidor do GitHub Enterprise, no GitLab, no Bitbucket Cloud ou no Azure Repos. Depois, automatize o build, o teste e a implantação do código no Microsoft Azure, na Google Cloud Platform ou na AWS (Amazon Web Services).

#### <a name="azure-pipelines-features"></a>Recursos do Azure Pipelines

- **Experiência simplificada baseada em tarefas para configurar um servidor de CI:** configure um servidor de CI para aplicativos móveis nativos (Android, iOS e Windows) e multiplataforma (Xamarin, Cordova e React Native).
- **Qualquer linguagem, plataforma e nuvem:** compile, teste e implante aplicativos Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android e iOS. Execute-o em paralelo no Linux, no macOS e no Windows. Implante-o em provedores de nuvem como o Azure, a AWS e a Google Cloud Platform. Distribua aplicativos móveis por meio de canais beta e lojas de aplicativos.
- **Suporte a contêiner nativo:** crie contêineres com facilidade e efetue push deles para qualquer registro. Implante contêineres em hosts independentes ou no Kubernetes.
- **Recursos e fluxos de trabalho avançados:** crie com facilidade cadeias de build e builds em fases. Obtenha suporte para YAML, integração de testes, portões de liberação, relatórios, entre outros.
- **Extensível:** use uma variedade de tarefas de build, teste e implantação criadas pela comunidade, que inclui centenas de extensões do Slack para o SonarCloud. Você pode, até mesmo, fazer a implantação de outros sistemas de CI, como o Jenkins. Os webhooks e as APIs REST podem ajudar você a fazer a integração.
- **Builds gratuitos hospedadas na nuvem:** Esses builds estão disponíveis para repositórios públicos e particulares.
- **Suporte para implantação em outros fornecedores de nuvem:** os fornecedores incluem a AWS e a Google Cloud Platform.

#### <a name="azure-pipelines-references"></a>Referências do Azure Pipelines

- [Introdução ao guia do Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuir seu aplicativo diretamente para lojas de aplicativos

Depois que o seu aplicativo estiver pronto para uso em produção e você quiser que ele seja usado publicamente, ele precisará ser enviado para lojas de aplicativos, nas quais ele poderá ser baixado pelos clientes. Há várias maneiras de distribuir seu aplicativo diretamente para lojas de aplicativos. 

### <a name="visual-studio-app-center-distribute-stores"></a>Repositórios do Visual Studio App Center Distribute

Com o [App Center Distribute](/appcenter/distribution/stores/), você pode publicar seus aplicativos móveis diretamente em lojas de aplicativos. Depois que o seu aplicativo estiver pronto para ser baixado pelos usuários, você poderá publicar os binários do aplicativo diretamente por meio do portal do Visual Studio App Center. 

Distribua-o diretamente para:

- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)

### <a name="apple-app-store"></a>Apple App Store

Na loja de aplicativos desenvolvida e mantida pela Apple, os usuários podem procurar e baixar aplicativos desenvolvidos para dispositivos iOS, macOS, watchOS e tvOS. Os desenvolvedores precisam enviar seus aplicativos iOS para a Apple App Store para uso público.

### <a name="google-play"></a>Google Play

O Google Play é a loja de aplicativos oficial para o sistema operacional Android, no qual os usuários podem procurar e baixar aplicativos desenvolvidos para dispositivos Android que são publicados por meio da Google.

### <a name="intune"></a>Intune

O [Microsoft Intune](/intune/app-management) é um serviço baseado em nuvem no espaço de gerenciamento de mobilidade empresarial que ajuda a permitir que a sua força de trabalho seja produtiva, mantendo os dados corporativos protegidos. Com o Intune, você pode:

- Gerenciar os dispositivos móveis e os computadores utilizados pela força de trabalho para acessar os dados da empresa.
- Gerenciar os aplicativos móveis utilizados pela força de trabalho.
- Proteger as informações da sua empresa, controlando a forma com a força de trabalho acessa e compartilha essas informações.
- Garantir que os dispositivos e os aplicativos estejam em conformidade com os requisitos de segurança da empresa.

## <a name="deploy-updates-directly-to-users-devices"></a>Implantar atualizações diretamente nos dispositivos dos usuários

### <a name="codepush"></a>CodePush

Com o [CodePush](/appcenter/distribution/codepush/) no App Center, os desenvolvedores do Apache Cordova e do React Native podem implantar atualizações de aplicativos móveis diretamente nos dispositivos dos usuários. Ele funciona como um repositório central no qual os desenvolvedores podem publicar determinadas atualizações, como JavaScript, HTML, CSS e alterações de imagem. Em seguida, os aplicativos podem consultar as atualizações no repositório usando os SDKs de cliente fornecidos. Dessa forma, você pode ter um modelo de participação mais determinístico e direto com os usuários, enquanto corrige bugs ou adiciona recursos secundários. Não é necessário recompilar um binário nem o redistribuir por meio de lojas de aplicativos públicos.

#### <a name="codepush-key-features"></a>Principais recursos do CodePush

- Os desenvolvedores do Cordova e do React Native podem implantar atualizações de aplicativos móveis diretamente nos dispositivos dos usuários sem liberá-los em uma loja.
- Útil para correção de bugs ou adição e remoção de recursos secundários que não exigem a recompilação do binário e a redistribuição dele por meio das respectivas lojas.

#### <a name="codepush-references"></a>Referências do CodePush

- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup)
- [Introdução ao CodePush no App Center](/appcenter/distribution/codepush/)
- [CLI do CodePush](/appcenter/distribution/codepush/cli)
