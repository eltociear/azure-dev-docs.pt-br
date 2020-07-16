---
title: JDKs do Java e suporte de médio e longo prazo para desenvolvimento do Azure
description: Este artigo fornece downloads e uma declaração do Suporte do Azure para desenvolvimento e execução de aplicativos Java.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-september2019, devx-track-java
ms.openlocfilehash: 5bffb4e4d2f68ef61ea96ededdf51ea98bb72d2a
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379830"
---
# <a name="java-long-term-support-and-medium-term-support-on-azure-and-azure-stack"></a>Suporte Java a longo prazo e a médio prazo no Azure e no Azure Stack

Os desenvolvedores de Java no Azure e Azure Stack podem criar e executar aplicativos Java de produção usando os builds do JDK [Azul Zulu para Azure – Edição Enterprise](https://www.azul.com/downloads/azure-only/zulu/) sem incorrer em custos adicionais de suporte. Você poderá usar qualquer runtime do Java que desejar no Azure. Contudo, quando você usa o Zulu, você recebe atualizações de manutenção gratuitas e pode criar tíquetes de suporte com a Microsoft.

Versões designadas como LTS (suporte de longo prazo) são as mesmas versões de LTS designadas pela Oracle e pela comunidade OpenJDK. Para versões do LTS, fornecemos pelo menos 8 anos de acesso a correções de bug, atualizações de segurança e outras correções (suporte de produção), conforme necessário. Também fornecemos 2 anos de suporte adicional destinado a aconselhar e auxiliar os usuários que estão migrando para uma versão mais recente do JDK (Suporte Estendido).

Para aquelas versões projetadas como MTS (suporte de médio prazo), fornecemos Suporte de Produção por pelo menos 1,5 ano após a disponibilidade geral da próxima versão do LTS. Também fornecemos 1 ano adicional de Suporte Estendido.

> [!div class="nextstepaction"]
> [Baixar e Instalar Java](java-jdk-install.md)

## <a name="long-term-support-lts"></a>LTS (suporte de longo prazo)

* [Java 11](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts)
* [Java 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts)
* [Java 7](https://www.azul.com/downloads/azure-only/zulu/?&version=java-7-lts)

## <a name="medium-term-support-mts"></a>MTS (suporte de médio prazo)

* [Java 13](https://www.azul.com/downloads/azure-only/zulu/?&version=java-13)

## <a name="technical-preview"></a>Visualização técnica

* [Java 14](https://www.azul.com/downloads/azure-only/zulu/?version=java-14)

## <a name="what-is-the-zulu-openjdk-for-azure"></a>O que é o Zulu OpenJDK para o Azure?

Builds do Azul Zulu para Azure – Edição Enterprise do OpenJDK são distribuições gratuitas, multiplataforma e prontas para produção do OpenJDK para Azure e Azure Stack. Eles têm o apoio da Microsoft e da Azul Systems. Essas distribuições são:

* Builds de software totalmente livre do OpenJDK empacotados como JDKs (Kits de Desenvolvimento Java), JRE (Java Runtime Environment) e JREs sem periféricos. Esses binários são totalmente compatíveis e estão em conformidade com builds comerciais do Java SE (Standard Edition) que podem ser usados com aplicativos Java ou componentes no Azure e no Azure Stack.
* Fornecidas com suporte de longo prazo, incluindo correções de bug, aprimoramentos de desempenho e patches de segurança.
* Disponíveis para desenvolvimento e execução de aplicativos Java no Windows, no Linux e no macOS.
* Disponíveis como imagens de contêiner no Docker Hub e máquinas virtuais (Windows e Linux) no Azure Marketplace.
* Usadas pelo Microsoft Azure para potencializar muitos serviços do Azure, como:
  * Serviço de Aplicativo no Windows
  * Serviço de Aplicativo no Linux
  * Funções do Azure
  * Azure Service Fabric
  * Azure HDInsight
  * Pesquisa Cognitiva do Azure
  * Azure DevOps
  * Azure Cloud Shell  

## <a name="supported-java-versions-and-update-schedule"></a>Versões com suporte de Java e agendamento de atualização

A Azul Systems fornece builds do [Azul Zulu para Azure – Edição Enterprise](https://www.azul.com/downloads/azure-only/zulu/) com suporte completo para todas as versões LTS (suporte de longo prazo) e MTS (suporte de médio prazo) do Java, incluindo Java SE 7, 8, 11 e 13. Para obter mais informações, consulte a [nota de imprensa da Azul](https://www.azul.com/press_release/free-java-production-support-for-microsoft-azure-azure-stack) e o roteiro do [Ciclo de Vida do Suporte ao Produto Azul](https://www.azul.com/products/azul_support_roadmap/).

|Versão do Java SE  |Com suporte até  |
|---------|----------|
|[![Logotipo do Java 7](media/supported-java-versions-java-7.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-7-lts) |Julho de 2023 (LTS)|
|[![Logotipo do Java 8](media/supported-java-versions-java-8.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts) |Dezembro de 2030 (LTS)|
|[![Logotipo do Java 11](media/supported-java-versions-java-11.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts) |Setembro de 2027 (LTS)|
|[![Logotipo do Java 13](media/supported-java-versions-java-13.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-13) |Março de 2023 (MTS)|
|[![Logotipo do Java 14](media/supported-java-versions-java-14.png)](https://www.azul.com/downloads/azure-only/zulu/?version=java-14) |**Visualização**|

As versões LTS e MTS do JDK têm atualizações de segurança trimestrais, correções de bug, atualizações críticas fora da faixa e patches conforme necessário. Esse suporte inclui backports para o Java 7 e 8 de atualizações de segurança e correções de bug que são relatadas em versões mais recentes do Java, como Java 11. O backporting garante a estabilidade e a segurança contínuas de versões mais antigas do Java. Os clientes do Azure podem obter essas atualizações de segurança e correções de bug da plataforma sem incorrer em nenhuma taxa de assinatura não planejada do Java SE.

No momento, o Azure Functions requer o Java 8 e o suporte para Java 11 ainda está em desenvolvimento.

## <a name="benefits-for-developers"></a>Benefícios para os desenvolvedores

As versões do JDK do Azul Zulu para Azure – Edição Enterprise:

- São compatíveis com a Microsoft e a Azul Systems, tendo o suporte delas.

   * Os binários do Zulu estão prontos para produção e têm o suporte da Microsoft e da Azul Systems.
   * O Zulu vem com LTS (suporte de longo prazo) gratuito para Java 7, 8 e 11 e com MTS (suporte de médio prazo) para Java 13. (O LTS será fornecido para Java 17 também.) Você pode atualizar versões do Java somente quando você precisa.
   * Há suporte para o Java 7 até julho de 2023. Há suporte para o Java 8 até dezembro de 2030. Há suporte para o Java 11 até setembro de 2027. Há suporte para o Java 13 até março de 2023.
   * A Microsoft está comprometida a executar o Zulu internamente em computadores que potencializam muitos serviços do Azure.

- Estão prontos para produção.

   * Software totalmente livre para os builds do OpenJDK.
   * Substituições prontas para muitas distribuições do Java SE.
   * JDK, JRE e JRE sem periféricos.
   * Java 7, 8, 11 e 13.
   * Verificado em conformidade com as especificações do Java SE por meio do OpenJDK Community TCK (Technology Compatibility Kit).
   * Incluem atualizações de produção para Java SE, incluindo correções de bugs, aprimoramentos de desempenho e patches de segurança para Java SE 7, 8, 11 e 13.

- Têm suporte para multiplataforma. O Zulu é compatível com binários para várias plataformas e versões:

   * Windows
     * 10
     * 8.1
     * 8
     * 7
   * Windows Server
     * 2016 R2
     * 2016
     * 2012 R2
     * 2012
     * 2008 R2
   * Linux
     * RHEL
     * CentOS
     * Ubuntu
     * SLES
     * Debian
     * Oracle Linux
   * Mac OS X
   * Entregue em vários tipos de pacote:
     * MSI, ZIP, tar, deb, RPM e DMG

     Imagens de contêiner do Docker certificadas para o JDK do Zulu, o JRE e o JRE sem periféricos em várias imagens de SO base estão disponíveis no Docker Hub:

     * [JDK](https://hub.docker.com/_/microsoft-java-jdk)
     * [JRE](https://hub.docker.com/_/microsoft-java-jre)
     * [JRE sem periféricos](https://hub.docker.com/_/microsoft-java-jre-headless)

- São gratuitos.

   * A Microsoft fornece tudo o que você precisa para criar e dimensionar aplicativos Java no Azure gratuitamente. Por meio do Zulu, você receberá atualizações de segurança e correções de bug de plataforma gratuitas para aplicativos Java.
   * [O Java Flight Recorder e o Zulu Mission Control](java-jdk-flight-recorder-and-mission-control.md) estão disponíveis no Zulu Java 8, 11 e posteriores.

- Incluem visualizações técnicas de versões não LTS/MTS.

   * As visualizações técnicas permitem que você teste progressivamente novos recursos conforme eles são entregues, em versões de curto prazo que acabarão sendo atualizadas para o Java 17 LTS.

- Incluem alterações com upstream no OpenJDK.
   * Os confirmadores da Azul Systems enviam alterações do Zulu por push para o OpenJDK. Essas confirmações tornam o repositório upstream abrangente e inclusivo.

Como sempre, os desenvolvedores Java podem trazer os próprios runtimes do Java, incluindo o Oracle JDK e o Red Hat JDK, para o Azure e usar a infraestrutura segura e os serviços repletos de recursos. A edição de produção do Oracle Java SE também está disponível para desenvolvedores de Java executando cargas de trabalho Java nas máquinas virtuais do Linux ou do Windows no Azure.

## <a name="use-for-local-development"></a>Uso para desenvolvimento local

Os desenvolvedores podem [baixar os Java JDKs para o Azure e o Azure Stack](https://www.azul.com/downloads/azure-only/zulu/) para uso nos ambientes de desenvolvimento. Há downloads disponíveis para Windows, Linux e macOS. Os desenvolvedores que trabalham no Linux também podem obter pacotes por meio dos gerenciadores de pacotes [yum](https://www.azul.com/downloads/azure-only/zulu/#yum-repo) e [apt](https://www.azul.com/downloads/azure-only/zulu/#apt-repo).

Para obter mais informações, confira [Imagens do Docker para Azure](java-jdk-docker-images.md).
