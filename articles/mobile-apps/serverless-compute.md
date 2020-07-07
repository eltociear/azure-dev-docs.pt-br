---
title: Criar um back-end de aplicativo móvel sem servidor com o Azure Functions e outros serviços
description: Saiba mais sobre os serviços de computação usados para criar um back-end sólido de aplicativo móvel sem servidor.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 297ed418ffb3a3b4101f2b9506b849a32ec506bc
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632428"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Crie componentes de back-end móvel com serviços de computação

Cada aplicativo móvel precisa ter um back-end que seja responsável pelo armazenamento de dados, pela lógica de negócios e pela segurança. O gerenciamento da infraestrutura para hospedar e executar o código de back-end exige que você dimensione, provisione e escale vários servidores. Você também precisa gerenciar as atualizações do sistema operacional e o hardware envolvido, além de aplicar patches de segurança. Em seguida, você precisa monitorar todos esses componentes da infraestrutura em relação ao desempenho, à disponibilidade e à tolerância a falhas. 

A arquitetura sem servidor é útil para esse tipo de cenário porque você não tem servidores e nenhuma atualização do sistema operacional ou atualizações de hardware ou software relacionadas para gerenciar. A arquitetura sem servidor oferece economia de tempo do desenvolvedor e redução de custos, o que significa tempo de comercialização mais rápido e energia concentrada na criação de aplicativos.

## <a name="benefits-of-compute"></a>Benefícios da computação

- A abstração de servidores significa que não há necessidade de se preocupar com hospedagem, aplicação de patches e segurança, o que permite que você se concentre exclusivamente no código.
- O dimensionamento instantâneo e eficiente garante que os recursos sejam provisionados automaticamente ou sob demanda em qualquer escala necessária.
- Alta disponibilidade e tolerância a falhas.
- A microcobrança garante que você seja cobrado apenas quando o seu código estiver realmente em execução.
- O código é executado na nuvem, escrito na linguagem de sua escolha.

Use os serviços a seguir para habilitar funcionalidades de computação sem servidor nos seus aplicativos móveis.

## <a name="azure-functions"></a>Funções do Azure

O [Azure Functions](https://azure.microsoft.com/services/functions/) é uma experiência de computação controlada por eventos que você pode usar para executar o código, escrito na linguagem de programação de sua escolha, sem se preocupar com servidores. Você não precisa gerenciar o aplicativo nem a infraestrutura para executá-lo. O Functions é dimensionado sob demanda. Você paga apenas pelo tempo em que o código é executado. As funções do Azure são uma ótima forma de implementar uma API para um aplicativo móvel. Elas são fáceis de serem implementadas e mantidas e podem ser acessadas por meio de HTTP.

### <a name="azure-functions-key-features"></a>Principais recursos do Azure Functions

- Controlado por eventos e escalonável, em que você pode usar gatilhos e associações para definir quando uma função é invocada e com quais dados ela se conectará.
- Traga suas dependências, porque o Functions dá suporte ao NuGet e ao npm. Portanto, você pode usar suas bibliotecas favoritas.
- Segurança integrada, de modo que você possa proteger funções disparadas por HTTP com provedores de OAuth como o Azure Active Directory, o Facebook, o Google, o Twitter e a conta Microsoft.
- Integração simplificada com diferentes ofertas de SaaS (software como serviço) e [serviços do Azure](/azure/azure-functions/functions-overview).
- Desenvolvimento flexível, de modo que você possa codificar suas funções diretamente no portal do Azure ou configurar a integração contínua e implantar o código por meio do GitHub, do Azure DevOps Services e de outras ferramentas de desenvolvimento compatíveis.
- O Functions Runtime é um software livre e está disponível no [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- Experiência de desenvolvimento aprimorada, em que você pode codificar, testar e depurar o código localmente usando seu editor preferido ou uma interface da Web fácil de usar com monitoramento com ferramentas integradas e funcionalidades internas de DevOps.
- Variedade de linguagens de programação e opções de hospedagem para desenvolvimento, como C#, Node.js, Java, JavaScript ou Python.
- O modelo de preços de pagamento por uso significa que você paga somente pelo tempo gasto na execução do seu código.

### <a name="azure-functions-references"></a>Referências do Azure Functions

- [Azure portal](https://portal.azure.com)
- [Documentação do Azure Functions](/azure/azure-functions/)
- [Guia do desenvolvedor do Azure Functions](/azure/azure-functions/functions-reference)
- [Inícios Rápidos](/azure/azure-functions/functions-create-first-function-vs-code)
- [Amostras](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Serviço de aplicativo do Azure

Com o [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), você pode criar e hospedar aplicativos Web e APIs RESTful na linguagem de programação de sua escolha sem gerenciar a infraestrutura. Ele oferece dimensionamento automático e alta disponibilidade, dá suporte ao Windows e ao Linux e permite implantações automatizadas do GitHub, do Azure DevOps ou de qualquer repositório Git.

### <a name="azure-app-service-key-features"></a>Principais recursos do Serviço de Aplicativo do Azure

- Suporte a várias linguagens e estruturas para ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Execute também o PowerShell e outros scripts ou executáveis como serviços em segundo plano.
- Otimização de DevOps por meio da integração e implantação contínuas com o Azure DevOps, o GitHub, o BitBucket, o Docker Hub ou o Registro de Contêiner do Azure. Gerencie seus aplicativos no Serviço de Aplicativo usando o Azure PowerShell ou a CLI (interface de linha de comando) multiplataforma.
- Escala global com alta disponibilidade para escala vertical ou horizontal de modo manual ou automático.
- Conexões com plataformas SaaS e dados locais para sua escolha entre mais de 50 conectores para sistemas empresariais, como o SAP, serviços de SaaS, como o Salesforce e serviços de Internet, como o Facebook. Acesse dados locais usando conexões híbridas e Redes Virtuais do Azure.
- O Serviço de Aplicativo do Azure é compatível com ISO, SOC e PCI. Autentique usuários com o Azure Active Directory ou com credenciais de mídia social, como o Google, o Facebook, o Twitter e a Microsoft. Crie restrições de endereço IP e gerencie identidades de serviço.
- Modelos de aplicativos para sua escolha entre uma lista abrangente de modelos de aplicativos no Azure Marketplace, como WordPress, Joomla e Drupal.
- A integração do Visual Studio a ferramentas dedicadas no Visual Studio simplifica o trabalho de criação, implantação e depuração.

### <a name="azure-app-service-references"></a>Referências do Serviço de Aplicativo do Azure

- [Azure portal](https://portal.azure.com/)
- [Documentação do Serviço de Aplicativo do Azure](/azure/app-service/)
- [Inícios Rápidos](/azure/app-service/app-service-web-get-started-dotnet)
- [Amostras](/azure/app-service/samples-cli)
- [Tutoriais](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

O [AKS (Serviço de Kubernetes do Azure)](https://azure.microsoft.com/services/kubernetes-service/) gerencia seu ambiente Kubernetes hospedado. O AKS acelera e facilita a implantação e o gerenciamento de aplicativos em contêineres sem conhecimento de orquestração de contêineres. Ele também elimina a carga de operações e manutenção contínuas. O AKS provisiona, atualiza e dimensiona recursos sob demanda, sem colocar seus aplicativos offline.

### <a name="azure-kubernetes-service-key-features"></a>Principais recursos do Serviço de Kubernetes do Azure

- Migre com facilidade os aplicativos existentes para contêineres e execute-os no AKS.
- Simplifique a implantação e o gerenciamento de aplicativos baseados em microsserviços.
- Proteja o DevOps para AKS a fim de obter o equilíbrio entre velocidade e segurança e fornecer o código mais rapidamente em escala.
- Use a escala de maneira fácil com o AKS e as Instâncias de Contêiner do Azure para provisionar pods dentro das Instâncias de Contêiner que são iniciadas em segundos.
- Implante e gerencie dispositivos IoT sob demanda.
- Treine modelos de machine learning com o uso de ferramentas como o TensorFlow e o KubeFlow.

### <a name="azure-kubernetes-service-references"></a>Referências do Serviço de Kubernetes do Azure

- [Azure portal](https://portal.azure.com/)
- [Documentação do Serviço de Kubernetes do Azure](/azure/aks/)
- [Inícios Rápidos](/azure/aks/kubernetes-walkthrough-portal)
- [Tutoriais](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Instâncias de Contêiner do Azure

As [Instâncias de Contêiner do Azure](https://azure.microsoft.com/services/container-instances/) são uma ótima solução para qualquer cenário que possa ser usado em contêineres isolados, como aplicativos simples, automação de tarefas e criação de trabalhos. Desenvolva aplicativos rapidamente sem gerenciar VMs.

### <a name="azure-container-instances-key-features"></a>Principais recursos das Instâncias de Contêiner do Azure

- Tempos de inicialização mais rápidos, pois as Instâncias de Contêiner podem iniciar contêineres no Azure em segundos, sem a necessidade de provisionar nem gerenciar VMs.
- Conectividade de IP público e nome DNS personalizado.
- Segurança em nível de hipervisor que garante que o seu aplicativo esteja tão isolado em um contêiner como estaria em uma VM.
- Tamanhos personalizados para a utilização ideal, permitindo especificações exatas de núcleos de CPU e memória. Você paga com base no que precisar e é cobrado por segundo, assim, é possível ajustar seus gastos eficientemente com base em suas necessidades reais.
- Armazenamento persistente para recuperar e persistir o estado. As Instâncias de Contêiner oferecem a montagem direta de compartilhamentos de Arquivos do Azure.
- Contêineres do Linux e do Windows agendados com a mesma API.

### <a name="azure-container-instances-references"></a>Referências das Instâncias de Contêiner do Azure

- [Azure portal](https://portal.azure.com/)
- [Documentação das Instâncias de Contêiner do Azure](/azure/container-instances/)
- [Inícios Rápidos](/azure/container-instances/container-instances-quickstart-portal)
- [Amostras](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Tutoriais](/azure/container-instances/container-instances-tutorial-prepare-app)