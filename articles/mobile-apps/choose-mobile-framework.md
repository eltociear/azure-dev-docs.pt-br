---
title: Escolher a plataforma de desenvolvimento de front-end para criar aplicativos cliente com o Visual Studio e os serviços do Azure
description: Saiba mais sobre as linguagens nativas e multiplataforma compatíveis para criar aplicativos cliente.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 8972c2b24334c964e00f5a3ccc27fa0c99e6f597
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632478"
---
# <a name="choose-a-mobile-development-framework"></a>Escolher uma estrutura de desenvolvimento móvel

Os desenvolvedores podem usar tecnologias do lado do cliente para criar aplicativos móveis por conta própria usando estruturas e padrões específicos para uma abordagem multiplataforma. Com base nos respectivos fatores de decisão, os desenvolvedores podem criar:

- Aplicativos nativos de plataforma única usando linguagens como o Objective-C e o Java
- Aplicativos multiplataforma usando o Xamarin, o .NET e o C#
- Aplicativos híbridos usando o Cordova e as variantes dele

## <a name="native-platforms"></a>Plataformas nativas

A criação de um aplicativo nativo exige linguagens de programação específicas da plataforma, SDKs, ambientes de desenvolvimento e outras ferramentas disponibilizadas pelos fornecedores do sistema operacional.

### <a name="ios"></a>iOS

Criado e desenvolvido pela Apple, o iOS é usado para criar aplicativos em dispositivos Apple, ou seja, o iPhone e o iPad.

- **Linguagens de programação**: Objective-C, Swift
- **IDE**: Xcode
- **SDK**: SDK do iOS

### <a name="android"></a>Android

Projetado pela Google e o sistema operacional mais popular do mundo, o Android é usado para criar aplicativos que podem ser executados em uma variedade de smartphones e tablets.

- **Linguagem de programação**: Java, Kotlin 
- **IDE**: Android Studio e ferramentas para desenvolvedores do Android 
- **SDK**: SDK do Android

### <a name="windows"></a>Windows

- **Linguagem de programação**: C#
- **IDE**: Visual Studio, Visual Studio Code
- **SDK**: SDK do Windows

#### <a name="native-platform-pros"></a>Vantagens da plataforma nativa

- Boa experiência do usuário
- Aplicativos dinâmicos com alto desempenho e a capacidade de interagir com bibliotecas nativas
- Aplicativos altamente seguros

#### <a name="native-platform-cons"></a>Desvantagens da plataforma nativa

- O aplicativo é executado em apenas uma plataforma
- Com uso mais intensivo de recursos para desenvolvedores e caro para criar um aplicativo
- Menor reutilização de código

## <a name="cross-platforms-and-hybrid-applications"></a>Multiplataformas e aplicativos híbridos

Os aplicativos multiplataforma capacitam você a escrever aplicativos móveis nativos uma vez, compartilhar o código e executá-los no iOS, no Android e no Windows.

### <a name="xamarin"></a>Xamarin

Pertencente à Microsoft, o [Xamarin](https://visualstudio.microsoft.com/xamarin/) é usado para criar aplicativos móveis robustos e multiplataforma em C#. O Xamarin tem uma biblioteca de classes e um runtime que funciona em várias plataformas, como o iOS, o Android e o Windows. Ele também compila aplicativos nativos (não interpretados) que oferecem alto desempenho. O Xamarin combina todas as capacidades das plataformas nativas e adiciona vários recursos avançados próprios.

- **Linguagem de programação**: C#
- **IDE**: Visual Studio no Windows ou no Mac

### <a name="react-native"></a>React Native

Lançado pelo Facebook em 2015, o [React Native](https://facebook.github.io/react-native/) é uma estrutura JavaScript [de software livre](https://github.com/facebook/react-native) usada para escrever aplicativos móveis reais renderizados nativamente para o iOS e o Android. Ele é baseado no React, a biblioteca JavaScript do Facebook para a criação de interfaces do usuário. Em vez de ser direcionado ao navegador, ele se destina a plataformas móveis. O React Native usa componentes nativos em vez de componentes da Web como blocos de construção.

- **Linguagem de programação**: JavaScript
- **IDE**: Visual Studio Code

### <a name="unity"></a>Unity

 O Unity é um mecanismo otimizado para a criação de jogos. Você pode usá-lo para criar aplicativos 2D ou 3D de alta qualidade com o C# para plataformas como o Windows, o iOS, o Android e o Xbox.

### <a name="cordova"></a>Cordova

O Cordova permite criar aplicativos híbridos usando as Ferramentas do Visual Studio para Apache Cordova ou o Visual Studio Code com extensões para o Cordova. Com a abordagem híbrida, você pode compartilhar componentes com sites e reutilizar aplicativos baseados em servidor Web com abordagens de aplicativos Web hospedados com base no Cordova.

#### <a name="cross-platform-pros"></a>Vantagens da multiplataforma

- Maior usabilidade de código com a criação de uma base de código para várias plataformas
- Atenda a um público-alvo maior em muitas plataformas
- Redução significativa no tempo de desenvolvimento
- Fácil de ser iniciada e atualizada

#### <a name="cross-platform-cons"></a>Desvantagens da multiplataforma

- Desempenho inferior
- Falta de flexibilidade
- Cada plataforma tem um conjunto exclusivo de recursos e funcionalidades para tornar o aplicativo nativo mais criativo
- Aumento do tempo de design de interface do usuário
- Limitação de ferramentas
