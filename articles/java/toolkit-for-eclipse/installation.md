---
title: Instalação do Kit de Ferramentas do Azure para o Eclipse
description: Saiba como instalar o Kit de Ferramentas do Azure para o plug-in Eclipse para criar e implantar aplicativos de nuvem no Azure.
documentationcenter: java
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.date: 08/25/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 27793d827b60a5977968529377b20c7033170ffe
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534644"
---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>Instalação do Kit de Ferramentas do Azure para o Eclipse

O Azure Toolkit for Eclipse fornece funcionalidade que permite criar, desenvolver, configurar, testar e implantar facilmente aplicativos Web Java leves, altamente disponíveis e escalonáveis e trabalhos do HDInsight Spark para o Azure usando o ambiente de desenvolvimento do Eclipse.

> [!NOTE] 
> 
> O Kit de Ferramentas do Azure para Eclipse é um projeto de código-fonte aberto, cujo código-fonte está disponível de acordo com a Licença do MIT no site do projeto no GitHub na seguinte URL: 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

Há dois métodos para instalar o Azure Toolkit for Eclipse: acessar o **Eclipse Marketplace** e usar a opção **Instalar novo software** no menu Ajuda. Ambos os métodos de instalação serão demonstrados nas seções a seguir.

## <a name="eclipse-marketplace"></a>Marketplace do Eclipse

O assistente do Marketplace do Eclipse no IDE do Eclipse permite que os usuários naveguem no [Marketplace do Eclipse](https://marketplace.eclipse.org/) e instalem soluções. As duas opções a seguir levam você ao Marketplace do Eclipse:

   * Arraste o botão a seguir para seu workspace do Eclipse em execução. Esse botão abre o Marketplace do Eclipse com o Azure Toolkit for Eclipse já selecionado.

      [![Arraste-o para o workspace do Eclipse* em execução. *Exige o Cliente do Eclipse Marketplace](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "Arraste-o para o workspace do Eclipse* em execução. *Exige o Cliente do Eclipse Marketplace")

   * No IDE do Eclipse, clique no menu **Ajuda**, navegue até **Marketplace do Eclipse**, pesquise "Azure Toolkit for Eclipse" e clique em **Instalar**.

      :::image type="content" source="media/installation/eclipse-marketplace-button.png" alt-text="Janela do Marketplace, menu Ajuda."::: 

1. Um assistente do Marketplace do Eclipse será exibido com instruções de instalação, incluindo uma lista de componentes que serão instalados. Verifique se todos os recursos estão selecionados e clique em **Confirmar >** .

   | Recurso | Descrição | 
   |---|---| 
   | **Plug-in do Application Insights para Java** | Permite que você use os serviços de registro em log e análise de telemetria do Azure para seus aplicativos e instâncias do servidor. | 
   | **Plug-in Comum do Azure** | Fornece a funcionalidade comum necessária para outros componentes do kit de ferramentas. | 
   | **Ferramentas de Contêiner do Azure para Eclipse** | Permite que você crie e implante um .WAR como um contêiner do Docker em uma máquina do docker. | 
   | **Contêineres do Azure para Eclipse** | Permite que você implante um artefato .WAR ou .JAR como um contêiner do Docker em uma máquina virtual do Azure. | 
   | **Gerenciador do Azure para Eclipse** | Fornece uma interface do gerenciador para gerenciar os recursos do Azure. | 
   | **Plug-in do Azure HDInsight para Java** | Permite o desenvolvimento de um aplicativo Apache Spark em Scala. |
   | **Microsoft JDBC Driver 6.1 para SQL Server** | Fornece a API JDBC para o SQL Server e o Banco de Dados SQL do Microsoft Azure para o Java Platform Enterprise Edition 8. | 
   | **Pacote para as Bibliotecas do Microsoft Azure para Java** | Fornece APIs para acessar os serviços do Microsoft Azure, como armazenamento, barramento de serviço, execução do serviço etc. | 
   | **Plug-in do WebApp para Eclipse** | Permite que você implante seus aplicativos Web como Serviços de Aplicativos do Azure. | 

1. No diálogo **Examinar Licenças** , leia os termos dos contratos de licença. Se você aceitar os termos dos contratos de licença, clique em **Aceito os termos dos contratos de licença** e clique em **Concluir**. 

   > [!NOTE]
   > Você pode verificar o progresso da instalação no canto inferior direito do workspace do Eclipse.

4. Após a conclusão da instalação, você será solicitado a reiniciar o IDE do Eclipse para aplicar a atualização de software. Clique em **Reiniciar Agora**.

## <a name="install-new-software"></a>Instalar novo software

Você pode instalar o Azure Toolkit for Eclipse diretamente no menu *Ajuda* na forma de um novo software.

1. Clique no menu **Ajuda** e em **Instalar Novo Software**.

   :::image type="content" source="media/installation/eclipse-install-software-button.png" alt-text="Instalar o novo software, menu Ajuda."::: 

1. Na caixa de diálogo **Software Disponível**, digite `http://dl.microsoft.com/eclipse/` na caixa de texto **Trabalhar com**.

1. No painel **Nome**, marque **Kit de Ferramentas do Azure para Java** e desmarque **Entrar em contato com todos os sites de atualização durante a instalação para encontrar o software necessário**. Sua tela será semelhante à seguinte:

   ![Instalação do Kit de Ferramentas do Azure para o Eclipse][02]

1. Se você expandir o **Azure Toolkit for Java**, verá uma lista de componentes que serão instalados; por exemplo:

   | Recurso | Descrição | 
   |---|---| 
   | **Plug-in do Application Insights para Java** | Permite que você use os serviços de registro em log e análise de telemetria do Azure para seus aplicativos e instâncias do servidor. | 
   | **Plug-in Comum do Azure** | Fornece a funcionalidade comum necessária para outros componentes do kit de ferramentas. | 
   | **Ferramentas de Contêiner do Azure para Eclipse** | Permite que você crie e implante um .WAR como um contêiner do Docker em uma máquina do docker. | 
   | **Contêineres do Azure para Eclipse** | Permite que você implante um artefato .WAR ou .JAR como um contêiner do Docker em uma máquina virtual do Azure. | 
   | **Gerenciador do Azure para Eclipse** | Fornece uma interface do gerenciador para gerenciar os recursos do Azure. | 
   | **Plug-in do Azure HDInsight para Java** | Permite o desenvolvimento de um aplicativo Apache Spark em Scala. |
   | **Microsoft JDBC Driver 6.1 para SQL Server** | Fornece a API JDBC para o SQL Server e o Banco de Dados SQL do Microsoft Azure para o Java Platform Enterprise Edition 8. | 
   | **Pacote para as Bibliotecas do Microsoft Azure para Java** | Fornece APIs para acessar os serviços do Microsoft Azure, como armazenamento, barramento de serviço, execução do serviço etc. | 
   | **Plug-in do WebApp para Eclipse** | Permite que você implante seus aplicativos Web como Serviços de Aplicativos do Azure. | 

1. Clique em **Próximo**. (Se você experimentar atrasos incomuns ao instalar o kit de ferramentas, certifique-se de que a opção **Contatar todos os sites de atualização durante a instalação para encontrar o software necessário** está desmarcada.)

1. No diálogo **Instalar Detalhes**, clique em **Avançar**.

1. No diálogo **Examinar Licenças** , leia os termos dos contratos de licença. Se você aceitar os termos dos contratos de licença, clique em **Aceito os termos dos contratos de licença** e clique em **Concluir**. (As etapas restantes supõem que você aceite os termos dos contratos de licença. Se você não aceitar os termos dos contratos de licença, saia do processo de instalação.)

   > [!NOTE]
   > Você pode verificar o progresso da instalação no canto inferior direito do workspace do Eclipse.

1. Se solicitado, reinicie o Eclipse para concluir a instalação e clique em **Reiniciar Agora**.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

<!-- IMG List -->
[02]: media/installation/eclipse-installation-02.png
