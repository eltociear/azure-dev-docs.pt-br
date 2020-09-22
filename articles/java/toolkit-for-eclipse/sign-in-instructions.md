---
title: Instruções de entrada para o Kit de Ferramentas do Azure para Eclipse
description: Saiba como se inscrever no Microsoft Azure usando o Kit de ferramentas do Azure para Eclipse.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: d0dbc16a16ca3a5ff367e6c67fceabcb37e2cce6
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534415"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-eclipse"></a>Instruções de entrada para o Kit de Ferramentas do Azure para Eclipse

O Kit de ferramentas do Azure para Eclipse fornece dois métodos para entrar em sua conta do Azure:

  - [Entre em sua conta do Azure com o Logon no Dispositivo](#sign-in-to-your-azure-account-by-device-login)
  - [Entre em sua conta do Azure com a Entidade de Serviço](#sign-in-to-your-azure-account-by-service-principal)

[Também são fornecidos métodos para **Sair**](#sign-out-of-your-azure-account).

[!INCLUDE [prerequisites](includes/prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>Entre em sua conta do Azure com o Logon no Dispositivo

Esta seção orienta você no processo de entrada do Azure por logon do dispositivo.

1. Abra seu projeto com o Eclipse.

1. Clique em **Ferramentas**, depois clique em **Azure** e, em seguida, clique em **Entrar**.

      :::image type="content" source="media/sign-in-instructions/eclipse-azure-signin.png" alt-text="Entre no Azure no IDE do Eclipse.":::

1. Na caixa de diálogo **Entrar no Azure**, selecione **Logon no Dispositivo** e clique em **Entrar**.

   ![A janela Entrar no Azure com o logon no dispositivo selecionado][I02]

1. Clique em **Copiar e Abrir** na caixa de diálogo **Logon no Dispositivo do Azure**.

> [!NOTE]
>
> Se o navegador não abrir, configure o Eclipse para usar um navegador externo, como o Internet Explorer, o Firefox ou o Chrome:
>
> 1. Abra Preferências -> Geral -> Navegador da Web -> Usar navegador da Web externo no Eclipse
>
> 2. Selecione o navegador que você prefere usar
>

1. No navegador, cole o código de dispositivo (que foi copiado quando você clicou em **Copiar e Abrir** na última etapa) e, em seguida, clique em **Avançar**.

1. Selecione sua conta do Azure e conclua os procedimentos de autenticação necessários para entrar.

1. Depois de entrar, feche o navegador e volte para o IDE do Eclipse. Na caixa de diálogo **Selecionar Assinaturas**, selecione as assinaturas que deseja usar e clique em **OK**.

## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Entre em sua conta do Azure com a Entidade de Serviço

Esta seção fornece uma orientarão pela criação de um arquivo de credenciais contendo os dados de sua entidade de serviço. Após a conclusão desse processo, o Eclipse usará automaticamente o arquivo de credenciais para entrar automaticamente no Azure quando você abrir o projeto.

1. Abra seu projeto com o Eclipse.

2. Clique em **Ferramentas**, depois clique em **Azure** e, em seguida, clique em **Entrar**.

      :::image type="content" source="media/sign-in-instructions/eclipse-azure-signin.png" alt-text="Entre no Azure no IDE do Eclipse.":::

3. Na janela **Entrar no Azure**, selecione **Entidade de Serviço**. Se você ainda não tiver o arquivo de autenticação de entidade de serviço, clique em **Novo** para criá-lo. Caso contrário, você pode clicar em **Procurar** abri-lo e ir para a etapa 8.

   ![A janela Entrada do Azure com a entidade de serviço selecionada][A02]

4. Clique em **Copiar e Abrir** na caixa de diálogo **Logon no Dispositivo do Azure**.

> [!NOTE]
>
> Se o navegador não abrir, configure o Eclipse para usar um navegador externo, como o IE ou o Chrome:
>
> 1. Abra Preferências -> Geral -> Navegador da Web -> Usar navegador da Web externo no Eclipse
>
> 2. Selecione o navegador que você prefere usar
>

5. No navegador, cole o código de dispositivo (que foi copiado quando você clicou em **Copiar e Abrir** na última etapa) e, em seguida, clique em **Avançar**.

6. Na janela **Criar Arquivos de Autenticação**, selecione as assinaturas que quer usar, escolha o diretório de destino e clique em **Iniciar**.

7. Na caixa de diálogo **Status de Criação da Entidade de Serviço**, clique em **OK** após a criação bem-sucedida dos arquivos.

8. O endereço do arquivo criado será automaticamente preenchido na janela **Entrar no Azure**, agora clique **Entrar**.

   ![Caixa de Diálogo de Logon do Azure][A06]

9. Por fim, na caixa de diálogo **Selecionar Assinaturas**, selecione as assinaturas que deseja usar e clique em **OK**.


## <a name="sign-out-of-your-azure-account"></a>Sair de sua conta do Azure

Depois que você configurou sua conta seguindo as etapas anteriores, será conectado automaticamente sempre que iniciar o Eclipse. No entanto, se desejar sair de sua conta do Azure, use as etapas a seguir.

1. No Eclipse, clique em **Ferramentas**, depois clique em **Azure** e, em seguida, clique em **Sair**.

2. Quando a caixa de diálogo **Saída do Azure** for exibida, clique em **Sim**.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->


<!-- IMG List -->

[I02]: media/sign-in-instructions/I02.png

[A02]: media/sign-in-instructions/A02.png
[A06]: media/sign-in-instructions/A06.png