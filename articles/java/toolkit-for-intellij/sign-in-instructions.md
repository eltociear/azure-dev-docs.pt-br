---
title: Instruções de entrada para o Kit de ferramentas do Azure para IntelliJ
description: Saiba como se inscrever no Microsoft Azure usando o Kit de ferramentas do Azure para IntelliJ.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 4447c9d2b09d0d004b8c858aceb31e6c7cee9493
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673982"
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

2. Abra a barra lateral **Azure Explorer** e, em seguida, clique no ícone **Entrar no Azure** na barra na parte superior (ou no menu do IDEA **Ferramentas/Azure/Entrada no Azure**).

   ![O comando de Entrada do IntelliJ Azure][I01]

3. Na caixa de diálogo **Entrar no Azure**, selecione **Logon no Dispositivo** e clique em **Entrar**.

   ![A janela Entrar no Azure com o logon no dispositivo selecionado][I02]

4. Clique em **Copiar e Abrir** na caixa de diálogo **Logon no Dispositivo do Azure**.

   ![A janela da caixa de diálogo Logon no Azure][I03]

5. No navegador, cole o código de dispositivo (que foi copiado quando você clicou em **Copiar e Abrir** na última etapa) e, em seguida, clique em **Avançar**.

   ![O navegador de logon do dispositivo][I04]

6. Na caixa de diálogo **Selecionar Assinaturas**, selecione as assinaturas que deseja usar e, em seguida, clique em **OK**.

   ![A caixa de diálogo Selecionar Assinaturas][I05]

## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Entre em sua conta do Azure com a Entidade de Serviço

Esta seção fornece uma orientarão pela criação de um arquivo de credenciais contendo os dados de sua entidade de serviço. Após a conclusão desse processo, o IntelliJ usará o arquivo de credenciais para entrar automaticamente no Azure quando você abrir o projeto.

1. Abra seu projeto com o IntelliJ IDEA.

1. Abra a barra lateral **Azure Explorer** e, em seguida, clique no ícone **Entrar no Azure** na barra na parte superior (ou no menu do IDEA **Ferramentas/Azure/Entrada no Azure**).
   ![O comando de Entrada do IntelliJ no Azure][A01]

1. Na janela **Entrada no Azure**, selecione **Entidade de Serviço** e, em seguida, clique em **Novo**.

   ![A janela Entrada do Azure com a entidade de serviço selecionada][A02]

1. Clique em **Copiar e Abrir** na caixa de diálogo **Logon no Dispositivo do Azure**.

   ![A janela da caixa de diálogo Logon no Azure][A03]

1. No navegador, cole o código de dispositivo (que foi copiado quando você clicou em **Copiar e Abrir** na última etapa) e, em seguida, clique em **Avançar**.

   ![O navegador de logon do dispositivo][A04]

1. Na janela **Criar Arquivos de Autenticação**, selecione as assinaturas que quer usar, escolha o diretório de destino e clique em **Iniciar**.

   ![A janela Criar Arquivos de Autenticação][A05]

1. Na caixa de diálogo **Status de Criação da Entidade de Serviço**, clique em **OK** após a criação bem-sucedida dos arquivos.

   ![A caixa de diálogo Status de Criação da Entidade de Serviço][A06]

1. Na janela **Entrar no Azure**, clique em **Entrar**. 

   ![Caixa de Diálogo de Logon do Azure][A07]

1. Na caixa de diálogo **Selecionar Assinaturas**, selecione as assinaturas que deseja usar e, em seguida, clique em **OK**.

   ![A caixa de diálogo Selecionar Assinaturas][A08]

> Depois de criar o arquivo de autenticação da entidade de serviço, você pode começar pela etapa 8, escolher o arquivo de autenticação e entrar.

## <a name="sign-out-of-your-azure-account"></a>Sair de sua conta do Azure

Após ter configurado sua conta seguindo as etapas anteriores, você será conectado automaticamente sempre que iniciar IntelliJ IDEA. No entanto, se desejar sair de sua conta do Azure, faça o seguinte.

* No IntelliJ IDEA, abra a barra lateral do Azure Explorer, clique no ícone de **Saída do Azure** e confirme (ou, no menu do IDEA, **Ferramentas/Azure/Saída do Azure**).

   ![O comando de Saída do IntelliJ Azure][L01]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- IMG List -->

[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png

[A01]: media/sign-in-instructions/A01.png
[A02]: media/sign-in-instructions/A02.png
[A03]: media/sign-in-instructions/A03.png
[A04]: media/sign-in-instructions/A04.png
[A05]: media/sign-in-instructions/A05.png
[A06]: media/sign-in-instructions/A06.png
[A07]: media/sign-in-instructions/A07.png
[A08]: media/sign-in-instructions/A08.png
[A09]: media/sign-in-instructions/A09.png

[L01]: media/sign-in-instructions/L01.png
[L02]: media/sign-in-instructions/L02.png
[L03]: media/sign-in-instructions/L03.png
