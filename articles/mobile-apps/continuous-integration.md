---
title: Automatizar o ciclo de vida dos seus aplicativos com o Visual Studio App Center e os serviços do Azure
description: Saiba mais sobre os serviços como o App Center que ajudam a configurar o build e a integração contínuos para seus aplicativos móveis.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 19b83e0dcc161c87e5a70eb6f8d4bbac29858c7b
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632458"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatizar o ciclo de vida dos seus aplicativos com criação e integração contínuas

Como desenvolvedores, vocês escrevem o código e fazem check-in dele no repositório de código, mas talvez os commits do check-in no repositório nem sempre sejam consistentes. Quando vários desenvolvedores trabalham no mesmo projeto, podem surgir problemas com a integração. As equipes podem se deparar com situações em que as coisas não funcionam, os bugs se acumulam e o desenvolvimento do projeto fica atrasado. Os desenvolvedores precisam esperar até que todo o código de software seja compilado e testado para verificar se há erros, o que torna o processo lento e menos iterativo. 

Com o build e a integração contínuos, os desenvolvedores podem simplificar os builds e testar o código fazendo commit das alterações no repositório de código-fonte e colocando testes e verificações no ambiente de build. Dessa forma, eles sempre executam testes no código. Todas as alterações feitas no código-fonte são compiladas continuamente sempre que há um commit feito no repositório. A cada check-in, o servidor de CI (integração contínua) valida e executa qualquer teste criado pelo desenvolvedor. Se os testes não tiverem êxito, o código será devolvido para outras alterações. Dessa forma, os desenvolvedores não interrompem os builds criados. Eles também não precisam executar todos os testes localmente nos computadores, o que aumenta a produtividade do desenvolvedor. 

## <a name="key-benefits"></a>Principais benefícios

- Automatize builds, testes e implantações para pipelines.
- Detecte bugs e corrija problemas logo no início para garantir taxas de lançamento mais rápidas.
- Faça commit do código com mais frequência e crie aplicativos rapidamente.
- Obtenha flexibilidade para alterar o código rapidamente sem problemas.
- Obtenha um tempo de comercialização mais rápido, de modo que apenas um código de boa qualidade seja disponibilizado.
- Faça pequenas alterações de código com mais eficiência, devido à integração simultânea de pequenas partes de código.
- Aumente a transparência e a responsabilidade da equipe para obter comentários contínuos dos clientes e da sua equipe.

Use os serviços a seguir para habilitar um pipeline de integração contínua nos seus aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center

O [App Center Build](/appcenter/build/) ajuda você a compilar os aplicativos nativos e multiplataforma nos quais sua equipe está trabalhando usando uma infraestrutura de nuvem segura. Você pode conectar com facilidade seu repositório no Visual Studio App Center e começar a compilar seu aplicativo na nuvem a cada commit. Você não precisa se preocupar com a configuração de servidores de build localmente, configurações complicadas e de um código que se baseia no computador de um colega de trabalho, mas não no seu.

Com o poder agregado dos serviços do Visual Studio App Center, você pode automatizar ainda mais seu fluxo de trabalho. Libere builds automaticamente para testadores e lojas de aplicativos públicos com o App Center Distribute. Execute também testes de interface do usuário automatizados em milhares de configurações reais de dispositivo e de sistema operacional na nuvem com o App Center Test.

### <a name="visual-studio-app-center-features"></a>Recursos do Visual Studio App Center

- Configure a integração contínua em minutos e crie aplicativos com mais frequência e rapidez.
- Integre-o ao GitHub, ao BitBucket, ao Azure DevOps e ao GitLab.
- Crie builds rápidos e seguros em computadores gerenciados e hospedados na nuvem.
- Habilite seus builds para iniciar o teste e confirmar se o aplicativo é compilado em dispositivos Android e iOS do mundo real.
- Obtenha suporte nativo e multiplataforma para iOS, Android, macOS, Windows, Xamarin e React Native.
- Personalize seus builds adicionando scripts pós-clonagem, pré-build e pós-build.

### <a name="visual-studio-app-center-references"></a>Referências do Visual Studio App Center

- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introdução ao App Center Build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines

 O [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), um serviço do Azure DevOps, é um serviço completo de integração contínua e de CD (entrega contínua) que funciona com o seu provedor Git preferido. Ele pode ser implantado na maioria dos principais serviços de nuvem, que incluem o Azure. Comece a trabalhar com o seu código no GitHub, no Servidor do GitHub Enterprise, no GitLab, no Bitbucket Cloud ou no Azure Repos. Depois, automatize o build, o teste e a implantação do código no Microsoft Azure, na Google Cloud Platform ou na AWS (Amazon Web Services).

### <a name="azure-pipelines-features"></a>Recursos do Azure Pipelines

- **Experiência simplificada baseada em tarefas para configurar um servidor de CI:** configure um servidor de CI para aplicativos móveis nativos (Android, iOS e Windows) e multiplataforma (Xamarin, Cordova e React Native), além de tecnologias de servidor baseadas na Microsoft e não Microsoft (Node.js, Java).
- **Qualquer linguagem, plataforma e nuvem:** compile, teste e implante aplicativos Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android e iOS. Execute-o em paralelo no Linux, no macOS e no Windows. Implante-o em provedores de nuvem como o Azure, a AWS e a Google Cloud Platform. Distribua aplicativos móveis por meio de canais beta e lojas de aplicativos.
- **Suporte a contêiner nativo:** crie contêineres com facilidade e efetue push deles para qualquer registro. Implante contêineres em hosts independentes ou no Kubernetes.
- **Fluxos de trabalho avançados:** crie com facilidade cadeias de build e builds em fases. Obtenha suporte para YAML, integração de testes, portões de liberação, relatórios, entre outros.
- **Extensível:** use uma variedade de tarefas de build, teste e implantação criadas pela comunidade, que inclui centenas de extensões do Slack para o SonarCloud. Você pode, até mesmo, fazer a implantação de outros sistemas de CI, como o Jenkins. Os webhooks e as APIs REST podem ajudar você a fazer a integração.
- **Builds gratuitos hospedadas na nuvem:** Esses builds estão disponíveis para repositórios públicos e particulares.
- **Suporte para implantação em outros fornecedores de nuvem:** os fornecedores incluem a AWS e a Google Cloud Platform.

### <a name="azure-pipelines-references"></a>Referências do Azure Pipelines

- [Introdução ao guia do Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
- [Inícios Rápidos](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Para ajudar você a escolher o serviço ideal para os builds do seu aplicativo, confira o artigo que compara o [App Center Build vs. Azure Pipelines](/appcenter/build/choose-between-services).
