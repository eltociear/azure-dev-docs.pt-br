---
title: Instalação do Kit de Ferramentas do Azure para IntelliJ
description: Saiba como instalar o Kit de Ferramentas do Azure para o plug-in IntelliJ para criar e implantar aplicativos de nuvem no Azure.
documentationcenter: java
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.date: 09/09/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: fe8b07257ff3a9fc5523d13dd13e19982103ab05
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534438"
---
# <a name="installing-the-azure-toolkit-for-intellij"></a>Instalação do Kit de Ferramentas do Azure para IntelliJ

O Kit de Ferramentas do Azure para IntelliJ fornece modelos e funcionalidade que permitem criar, desenvolver, testar e implantar com facilidade aplicativos de nuvem ao Azure usando o ambiente de desenvolvimento IDEA do IntelliJ.

> [!NOTE] 
> 
> O Kit de Ferramentas do Azure para IntelliJ é um projeto de código-fonte aberto, cujo código-fonte está disponível de acordo com a Licença do MIT no site do projeto no GitHub na seguinte URL: 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

Há dois métodos de instalação do Kit de Ferramentas do Azure para IntelliJ: usando a caixa de diálogo **Configurações** e o menu **Configurar** na tela inicial. Ambos os métodos de instalação serão demonstrados nas etapas a seguir.

## <a name="prerequisites"></a>Pré-requisitos

O Azure Toolkit for IntelliJ requer os seguintes componentes de software:

* Um JDK (Java Development Kit) 8+ instalado, por exemplo: [OpenJDK](https://openjdk.java.net/) ou [Oracle JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Um [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition ou Community Edition instalado

> [!NOTE]
> 
> A página [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) no repositório de plug-in JetBrains lista as compilações compatíveis com o kit de ferramentas.
> 

<!--
> [!IMPORTANT]
> 
> If you are using the Azure Toolkit for IntelliJ on Windows, the toolkit requires installing the Azure SDK 2.9.6 or later in order to use the Azure emulator. You have two options for installing the Azure SDK:
> 
> * You can download and install the Azure SDK by using the [Web Platform Installer (WebPI)](https://go.microsoft.com/fwlink/?LinkID=252838).
> * If you do not have the Azure SDK installed when you create your first Azure deployment project, you will be prompted to automatically download install the requisite version of the Azure SDK.
> 
> Note that the Azure SDK is only required on Windows.
> 
-->


## <a name="from-the-settings-dialog-box"></a>Na caixa de diálogo Configurações

1. Na barra de ferramentas do IntelliJ, clique em **Arquivo** e em **Configurações**.

1. No menu de navegação à esquerda da caixa de diálogo Configurações, clique em **Plug-ins**.

1. Na barra de pesquisa **Marketplace**, digite "Azure" para filtrar a lista de plug-ins. Selecione **Azure Toolkit for IntelliJ** e clique em **Instalar**. Leia a *Observação de Privacidade de Plug-ins de Terceiros* do IntelliJ e clique em **Aceitar**.

   :::image type="content" source="media/installation/03-intellij-search-plugin.png" alt-text="Pesquise o plug-in Azure Toolkit for IntelliJ."::: 

1. Quando a instalação for concluída, clique em **Reiniciar IDE**.

1. Quando for solicitado a reiniciar o IDEA do IntelliJ, clique em **Reiniciar**.
   
   :::image type="content" source="media/installation/07-restart-intellij.png" alt-text="Reinicie o IDEA do IntelliJ."::: 

## <a name="from-the-start-screen"></a>Na tela Inicial

1. Na tela inicial do IDEA do IntelliJ, clique em **Configurar** e em **Plug-ins**.

   :::image type="content" source="media/installation/01-intellij-configure-dropdown.png" alt-text="Plug-ins da tela inicial."::: 

1. Na barra de pesquisa **Marketplace**, digite "Azure" para filtrar a lista de plug-ins. Selecione **Azure Toolkit for IntelliJ** e clique em **Instalar**. Leia a *Observação de Privacidade de Plug-ins de Terceiros* do IntelliJ e clique em **Aceitar**.

   :::image type="content" source="media/installation/01-intellij-start-screen-marketplace.png" alt-text="Marketplace de plug-ins na tela inicial.":::

1. Quando a instalação for concluída, clique em **Reiniciar IDE**.

1. Quando for solicitado a reiniciar o IDEA do IntelliJ, clique em **Reiniciar**.
   
   :::image type="content" source="media/installation/01-intellij-start-screen-marketplace-restart.png" alt-text="Reinicie para instalar da tela inicial.":::

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [additional-resources](includes/additional-resources.md)]

