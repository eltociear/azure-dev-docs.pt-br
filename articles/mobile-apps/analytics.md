---
title: Entender o uso do aplicativo móvel e o comportamento do usuário com o Visual Studio App Center e os serviços do Azure
description: Saiba mais sobre os serviços como o App Center que ajudam você a tomar decisões empresariais inteligentes entendendo como os usuários usam seu aplicativo móvel.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 744e41200a5f7e5ff898e7a0786a4284de176b7a
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632588"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analisar e entender o uso do aplicativo móvel

Você entende bem como os usuários usam seus aplicativos? Quantos usuários ativos seu aplicativo tem e como o uso é alterado ao longo do tempo? Quais recursos eles estão usando e quais são mais usados? Onde esses usuários estão localizados? Quantos usuários estão usando a última versão do aplicativo? Todas essas perguntas são importantes para entender como transformar seu aplicativo em um negócio bem-sucedido. Para responder a esses tipos de perguntas de análise de uso, você precisará coletar os dados de uso dos seus aplicativos.

Quanto mais você examinar os dados, mais poderá encontrar maneiras de aprimorar seu aplicativo e manter os usuários satisfeitos. É importante usar dados para encontrar informações práticas e manter os usuários satisfeitos.

## <a name="importance-of-analytics"></a>Importância da análise

- Entenda os usuários, como eles interagem com o seu aplicativo e o que traz esses usuários de volta para ajustar seu aplicativo e proporcionar ótimas experiências e expandir seus negócios.
- Acompanhe as métricas de uso para tomar decisões informadas sobre como comercializar seu aplicativo e atender melhor aos clientes.
- Avalie o desempenho do aplicativo.
- Saiba quais partes do aplicativo geram valor e desempenho.
- Obtenha insights controlados por dados sobre problemas relacionados à rotatividade e à retenção.

Use os serviços a seguir para habilitar a análise de aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center

A [Análise do App Center](/appcenter/analytics/) permite que você aumente seu público-alvo concentrando-se no que é importante. Ela oferece relatórios e insights detalhados sobre sessões de usuário, principais dispositivos, versões de sistema operacional e análise comportamental. Crie eventos personalizados com facilidade para controlar qualquer item com a análise extensiva do aplicativo.

### <a name="visual-studio-app-center-features"></a>Recursos do Visual Studio App Center

- Acompanhe os padrões de uso, a adoção do usuário e outras métricas de participação gratuitamente.
- Identifique tendências, comportamento do usuário e participação por meio de eventos personalizados.
- Obtenha métricas prontas para uso e informações detalhadas sobre o uso do aplicativo (diário, semanal, mensal), sessões, propriedades do dispositivo e dados demográficos do usuário em um só painel.
- Exporte continuamente todos os dados do App Center Analytics para o Azure a fim de obter retenção ilimitada. O App Center Analytics dá suporte à exportação para o Armazenamento de Blobs do Azure e o Azure Application Insights.
- Integre-a ao Azure Application Insights para obter insights ainda mais aprofundados, como retenção, análise de funil e coortes.
- Use a integração de uma linha do SDK para começar a usá-la em minutos.
- Obtenha suporte de plataforma para iOS, Android, macOS, tvOS, Xamarin, React Native, Unity e Cordova.

### <a name="visual-studio-app-center-references"></a>Referências do Visual Studio App Center

- [Inscreva-se no App Center](https://appcenter.ms/signup)
- [Introdução à Análise do App Center](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor

O Azure Monitor inclui o [Application Insights](/azure/azure-monitor/app/app-insights-overview), que fornece ferramentas para coletar e analisar a telemetria visando maximizar o desempenho e monitorar seu aplicativo móvel. Aproveite o Application Insights usando o App Center Analytics para configurar a exportação para o Application Insights. O Application Insights pode consultar, segmentar, filtrar e analisar a telemetria de evento personalizado por meio dos seus aplicativos, além das ferramentas de análise oferecidas pelo App Center.

### <a name="azure-monitor-features"></a>Recursos do Azure Monitor

- Consulte a telemetria de evento personalizado.
- Filtre a telemetria de evento com funcionalidades avançadas de segmentação.
- Analise padrões de conversão, retenção e navegação no aplicativo. Você pode usar:
  - Funis para analisar e monitorar as taxas de conversão.
  - Retenção para analisar o nível de retenção dos usuários ao longo do tempo alcançado pelo aplicativo.
  - Pastas de trabalho para combinar visualizações e texto em um relatório que pode ser compartilhado.
  - Coortes para nomear e salvar grupos de usuários ou eventos específicos, de modo que eles possam ser referenciados com facilidade por outras ferramentas de análise.

### <a name="azure-monitor-references"></a>Referências do Azure Monitor

- [Azure portal](https://portal.azure.com/)
- [Analisar seu aplicativo móvel com o App Center e o Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Usar o App Center Analytics com o Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab

O [Azure PlayFab](https://playfab.com/) oferece uma plataforma de back-end completa com serviços de jogos, análise em tempo real e o LiveOps necessário para criar jogos conectados à nuvem de nível mundial. Esses serviços reduzem as barreiras de lançamento para desenvolvedores de jogos. Eles oferecem soluções de desenvolvimento econômicas para estúdios de grande e pequeno porte que são dimensionadas com os respectivos jogos. Os serviços podem ajudar os estúdios a envolver, reter e monetizar jogadores. Com o PlayFab, os desenvolvedores podem usar a nuvem inteligente para criar e operar jogos, analisar dados de jogos e aprimorar as experiências gerais dos jogos.

### <a name="azure-playfab-features"></a>Recursos do Azure PlayFab

- Monitore painéis em tempo real.
- Avalie o desempenho do jogo por meio das principais métricas.
- Examine resumos do desempenho diário e mensal do seu jogo por meio de relatórios gerados automaticamente. Veja os relatórios no Gerenciador de Jogos e baixe-os ou receba-os na sua caixa de entrada diariamente.
- Use testes A/B para executar experimentos e determinar a configuração ideal para uma variável específica.
- Use a segmentação para os jogadores a fim de definir agrupamentos automatizados de jogadores.

### <a name="azure-playfab-references"></a>Referências do Azure PlayFab

- [Portal do PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analytics](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Inícios Rápidos](/gaming/playfab/#pivot=documentation&panel=quickstarts)
