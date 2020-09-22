---
title: Instruções de entrada para o Kit de ferramentas do Azure para IntelliJ
description: Saiba como se inscrever no Microsoft Azure usando o Kit de ferramentas do Azure para IntelliJ.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 2891b0c09c43b652fd7dd41e354290c2821bad46
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534547"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>Instruções de entrada para o Kit de ferramentas do Azure para IntelliJ

Após [instalado](https://www.jetbrains.com/help/idea/managing-plugins.html), o [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) fornece dois métodos para entrar em sua conta do Azure:

  - [Entre em sua conta do Azure com o Logon no Dispositivo](#sign-in-to-your-azure-account-by-device-login)
  - [Entre em sua conta do Azure com a Entidade de Serviço](#sign-in-to-your-azure-account-by-service-principal)

[Também são fornecidos métodos para **Sair**](#sign-out-of-your-azure-account).

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>Entre em sua conta do Azure com o Logon no Dispositivo

Para entrar no Azure por logon no dispositivo, faça o seguinte:

1. Abra seu projeto com o IntelliJ IDEA.

1. Abra a barra lateral **Azure Explorer** e, em seguida, clique no ícone **Entrar no Azure** na barra na parte superior (ou, no menu do IntelliJ, navegue para **Ferramentas > Azure > Entrar no Azure**).

   ![O comando de Entrada do IntelliJ Azure][I01]

1. Na caixa de diálogo **Entrar no Azure**, selecione **Logon no Dispositivo** e clique em **Entrar**.

   ![A janela Entrar no Azure com o logon no dispositivo selecionado][I02]

1. Clique em **Copiar e Abrir** na caixa de diálogo **Logon no Dispositivo do Azure**.

1. No navegador, cole o código de dispositivo (que foi copiado quando você clicou em **Copiar e Abrir** na última etapa) e, em seguida, clique em **Avançar**.

1. Selecione sua conta do Azure e conclua os procedimentos de autenticação necessários para entrar.

1. Na caixa de diálogo **Selecionar Assinaturas**, selecione as assinaturas que deseja usar e, em seguida, clique em **OK**.


## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Entre em sua conta do Azure com a Entidade de Serviço

Esta seção fornece uma orientarão pela criação de um arquivo de credenciais contendo os dados de sua entidade de serviço. Após a conclusão desse processo, o IntelliJ usará o arquivo de credenciais para entrar automaticamente no Azure quando você abrir o projeto.

1. Abra seu projeto com o IntelliJ IDEA.

1. Abra a barra lateral **Azure Explorer** e, em seguida, clique no ícone **Entrar no Azure** na barra na parte superior (ou, no menu do IntelliJ, navegue para **Ferramentas > Azure > Entrar no Azure**).

   ![O comando de Entrada do IntelliJ Azure][I01]

1. Na janela **Entrada no Azure**, selecione **Entidade de Serviço** e, em seguida, clique em **Novo**.

   ![A janela Entrada do Azure com a entidade de serviço selecionada][A02]

1. Clique em **Copiar e Abrir** na caixa de diálogo **Logon no Dispositivo do Azure**.

1. No navegador, cole o código de dispositivo (que foi copiado quando você clicou em **Copiar e Abrir** na última etapa) e, em seguida, clique em **Avançar**.

1. Selecione sua conta do Azure e conclua os procedimentos de autenticação necessários para entrar. Após a autenticação, feche o navegador e volte para o IntelliJ.

1. Na janela **Criar Arquivos de Autenticação**, selecione as assinaturas que quer usar, escolha o diretório de destino e clique em **Iniciar**.

1. Na caixa de diálogo **Status de Criação da Entidade de Serviço**, clique em **OK** após a criação bem-sucedida dos arquivos.

1. Na janela **Entrar no Azure**, clique em **Entrar**. 

1. Na caixa de diálogo **Selecionar Assinaturas**, selecione as assinaturas que deseja usar e, em seguida, clique em **OK**.

   > [!TIP]
   > Depois de criar o arquivo de autenticação da entidade de serviço, você pode começar pela etapa 3, escolher o arquivo de autenticação e entrar.

## <a name="sign-out-of-your-azure-account"></a>Sair de sua conta do Azure

Após ter configurado sua conta seguindo as etapas anteriores, você será conectado automaticamente sempre que iniciar IntelliJ IDEA. 

No entanto, se você quiser sair de sua conta do Azure, navegue até a barra lateral do Azure Explorer, clique no ícone **Sair do Azure** (ou, no menu do IntelliJ, navegue até **Ferramentas > Azure > Sair do Azure**).


## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- IMG List -->

[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png

[A02]: media/sign-in-instructions/A02.png

