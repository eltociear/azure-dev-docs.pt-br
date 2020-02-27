---
title: JDKs do Java e suporte de longo prazo para desenvolvimento do Azure
description: Downloads e instrução de suporte do Azure para desenvolver e executar aplicativos Java.
ms.topic: conceptual
ms.date: 04/09/2019
ms.custom: seo-java-september2019
ms.openlocfilehash: 5a54b81e1df649eea6716682059744a28ecff200
ms.sourcegitcommit: 6ffa53b933da524e09911b164bba8515722bfa91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77629395"
---
# <a name="java-long-term-support-for-azure-and-azure-stack"></a>Suporte de longo prazo do Java para o Azure e o Azure Stack

Os desenvolvedores de Java no Azure e Azure Stack podem criar e executar aplicativos Java de produção usando os builds do JDK [Azul Zulu para Azure – Edição Enterprise](https://www.azul.com/downloads/azure-only/zulu/) sem incorrer em custos adicionais de suporte. É possível usar qualquer runtime do Java que desejar no Azure, mas quando você usa o Zulu, você recebe atualizações de manutenção gratuitas e pode criar problemas de suporte com a Microsoft.

> [!div class="nextstepaction"]
> [Baixar e Instalar Java](java-jdk-install.md)

## <a name="long-term-support-lts"></a>LTS (suporte de longo prazo)

* [Java 11](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts)
* [Java 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts)
* [Java 7](https://www.azul.com/downloads/azure-only/zulu/?&version=java-7-lts)

## <a name="technical-preview"></a>Visualização técnica

* [Java 13](https://www.azul.com/downloads/azure-only/zulu/?&version=java-13)

## <a name="what-is-the-zulu-openjdk-for-azure"></a>O que é o Zulu OpenJDK para o Azure?

Builds do Azul Zulu para Azure – Edição Enterprise do OpenJDK são uma distribuição sem custo, multiplataforma e pronta para produção do OpenJDK para Azure e Azure Stack da Microsoft e da Azul Systems. Essas distribuições são:

* Builds 100% software livre do OpenJDK empacotados como JDKs (Kits de Desenvolvimento Java), JRE (Java Runtime Environment) e JREs Sem Periféricos. Esses binários são totalmente compatíveis e estão em conformidade com builds comerciais do Java SE (Standard Edition) que podem ser usados com aplicativos Java ou componentes no Azure e no Azure Stack.
* Fornecidas com suporte de longo prazo, incluindo correções de bug, aprimoramentos de desempenho e patches de segurança.
* Disponíveis para desenvolver e executar aplicativos Java no Windows, no Linux e no MacOS.
* Disponíveis como imagens de contêiner no Docker Hub e máquinas virtuais (Windows e Linux) no Azure Marketplace.
* Usadas pelo Microsoft Azure para potencializar muitos serviços do Azure, como:
  * Serviço de Aplicativo Windows
  * Serviço de Aplicativo Linux
  * Funções
  * Service Fabric
  * HDInsight
  * Search
  * Azure DevOps
  * Cloud Shell  

## <a name="supported-java-versions-and-update-schedule"></a>Versões com suporte de Java e agendamento de atualização

A Azul Systems fornece builds do [Azul Zulu para Azure – Edição Enterprise](https://www.azul.com/downloads/azure-only/zulu/) com suporte completo para todas as versões com LTS (suporte de longo prazo) do Java, começando com o Java SE 7, 8 e 11. Mais informações podem ser encontradas no [comunicado à imprensa da Azul](https://www.azul.com/press_release/free-java-production-support-for-microsoft-azure-azure-stack).

|Java SE LTS  |Suporte até  |
|---------|----------|
|[![Versão compatível do Java – Java 7](../media/jdk/supported-java-versions-java-7.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-7-lts) |Julho de 2023 |
|[![Versão compatível do Java – Java 8](../media/jdk/supported-java-versions-java-8.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts) |Março de 2025|
|[![Versão compatível do Java – Java 11](../media/jdk/supported-java-versions-java-11.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts) |Setembro de 2026|
|[![Versão compatível do Java – Java 13](../media/jdk/supported-java-versions-java-13.png)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-13) |**VERSÃO PRÉVIA**|

Essas versões do JDK têm atualizações de segurança trimestrais, correções de bugs e atualizações e patches não planejados críticos conforme necessário.  Esse suporte inclui a portabilidade de atualizações de segurança e correções de bugs para Java 7 e 8 relatadas nas versões mais recentes do Java, como Java 11, o que garante a estabilidade e a segurança contínuas de versões mais antigas do Java.  Os clientes do Azure podem obter essas atualizações de segurança e correções de bug da plataforma sem incorrer em nenhuma taxa de assinatura não planejada do Java SE.

A Azul Systems mantém um [roteiro do Java SE](https://www.azul.com/products/azul_support_roadmap/) para essas versões.

## <a name="benefits-for-developers"></a>Benefícios para os desenvolvedores

As versões do JDK do Azul Zulu para Azure – Edição Enterprise são:

1. Compatíveis com a Microsoft e a Azul Systems

   * Os binários do Zulu estão prontos para produção e têm o suporte da Microsoft e da Azul Systems
   * O Zulu vem com LTS (suporte de longo prazo) sem custo nenhum para Java 7, 8 e 11. (LTS será fornecido para Java 17 também.) Você pode atualizar versões do Java somente quando você precisa.
   * O Java 7 terá suporte até julho de 2023. Os Java 8 e 11 terão suporte além de 2024.
   * A Microsoft está comprometida a executar o Zulu internamente em computadores que potencializam muitos serviços do Azure.

2. Prontas para produção

   * 100% open-source para seus builds do OpenJDK.
   * Substituições prontas para muitas distribuições do Java SE.
   * JDK, JRE e JRE sem periféricos
   * Java 7, 8 e 11
   * Em conformidade verificada com as especificações de Java SE usando o TCK (Kit de Compatibilidade de Tecnologia) da Comunidade do OpenJDK.
   * Os desenvolvedores continuarão a receber atualizações de produção para Java SE, incluindo correções de bugs, aprimoramentos de desempenho e patches de segurança para Java SE 7, 8 e 11.

3. Compatíveis com multiplataforma. O Zulu é compatível com binários para várias plataformas e versões, incluindo:

   * Windows Client
     * 10
     * 8.1
     * 8, 7
   * Windows Server
     * 2016R2
     * 2016
     * 2012 R2
     * 2012
     * 2008 R2
   * Linux, incluindo
     * RHEL
     * CentOS
     * Ubuntu
     * SLES
     * Debian
     * Oracle Linux
   * Mac OS X
   * entregue em vários tipos de pacote:
     * MSI, ZIP, TAR, DEB, RPM e DMG

    Imagens de contêiner do Docker certificadas para o Zulu JDK, JRE e JRE sem periféricos em várias imagens de SO base estão disponíveis no Docker.

    Hub:

    * [JDK](https://hub.docker.com/_/microsoft-java-jdk)
    * [JRE](https://hub.docker.com/_/microsoft-java-jre)
    * [JRE sem periféricos](https://hub.docker.com/_/microsoft-java-jre-headless)

4. Sem custo

   * A Microsoft fornece tudo de que você precisa para compilar e dimensionar aplicativos Java no Azure sem custo para você. Por meio do Zulu, você receberá atualizações de segurança e correções de bug de plataforma gratuitas para aplicativos Java sem nenhuma taxa.
   * [O Flight Recorder Java e o Mission Control](java-jdk-flight-recorder-and-mission-control.md) estão disponíveis no Zulu Java 8, 11 e 12 (Versão Prévia).

5. Visualização técnica de versões não LTS

   * Visualizações técnicas dão oportunidades para testar progressivamente novos recursos conforme eles são entregues em versões de curto prazo que acabarão passando para Java 17 LTS.

6. As alterações do OpenJDK são transmitidas para cima

   * Os confirmadores da Azul Systems enviam por push as alterações do OpenJDK ao Zulu, o que torna o repositório upstream abrangente e inclusivo.

Como sempre, os desenvolvedores Java podem trazer seus próprios tempos de execução Java, incluindo o Oracle JDK e o Red Hat JDK, para o Azure e usar a infraestrutura segura e os serviços repletos de recursos. A edição de produção do Oracle Java SE também está disponível para desenvolvedores de Java executando cargas de trabalho Java nas máquinas virtuais do Linux ou do Windows no Azure.

## <a name="use-for-local-development"></a>Uso para desenvolvimento local

Os desenvolvedores podem [baixar](https://www.azul.com/downloads/azure-only/zulu/) os JDKs do Java para o Azure e o Azure Stack para uso nos ambientes de desenvolvimento. Há downloads disponíveis para Windows, Linux e macOS. Os desenvolvedores que trabalham no Linux também podem obter pacotes por meio dos gerenciadores de pacotes [yum](https://www.azul.com/downloads/azure-only/zulu/#yum-repo) e [apt](https://www.azul.com/downloads/azure-only/zulu/#apt-repo).

Para obter mais diretrizes, confira [Imagens do Docker para o Azure](java-jdk-docker-images.md).
