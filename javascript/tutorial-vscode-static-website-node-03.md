---
title: Criar uma conta de Armazenamento do Azure para um site do Node.js estático usando o Visual Studio Code
description: Parte 3 do tutorial, criar uma conta de Armazenamento do Azure
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 4adca67f850497777abce7d550e39532e59257d9
ms.sourcegitcommit: a32ca0946275165ce24216c6fa243ec21d6c9193
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80890825"
---
# <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

[Etapa anterior: Criar o aplicativo](tutorial-vscode-static-website-node-02.md)

Nesta etapa, você cria uma conta de Armazenamento do Azure, que serve como um armazenamento de arquivo simples (ou CDN) com um servidor Web interno. Esse servidor interno torna o Armazenamento do Azure uma ótima opção para hospedar rapidamente sites estáticos.

1. Na pasta `my-static-app` criada na etapa anterior, inicie o Visual Studio Code para que ele abra essa pasta automaticamente:

    ```bash
    code .
    ```

1. No VS Code, selecione o logotipo do Azure para abrir o gerenciador do **Azure**. Em **Armazenamento do Azure**, clique com o botão direito do mouse em sua assinatura do Azure e escolha **Criar Conta de Armazenamento**:

    ![Criar Conta de Armazenamento no VS Code](media/static-website/create-storage-account.png)

1. No prompt "Inserir o nome da nova conta de armazenamento", insira um nome globalmente exclusivo de sua Conta de Armazenamento e pressione Enter. Os caracteres válidos para um nome de aplicativo são "a-z" e "0-9".

    > [!NOTE]
    > Isso cria uma conta de armazenamento e um grupo de recursos com o mesmo nome. A conta de armazenamento será colocada automaticamente no Oeste dos EUA. Se você quiser especificar o grupo de recursos e a localização, selecione a opção "Criar Conta de Armazenamento (Avançado)" no menu de contexto.

1. Enquanto a conta de armazenamento é criada, o progresso aparece no painel **Saída** do VS Code:

    ![Janela de saída do VS Code ](media/static-website/output-storage.png)

1. Quando a Conta de armazenamento for criada, será exibida uma mensagem informando que a hospedagem de site estática foi habilitada para a conta de armazenamento.

    ![Criar uma conta de armazenamento](media/static-website/static-website-enabled-notification.png)

    > [!IMPORTANT]
    > Usamos *index.html* para o documento de erro porque os SPAs (aplicativos de página única) modernos, como React, Angular e Vue, tratam dos erros de rota no cliente. Para sites estáticos clássicos, use uma página de erro 404 personalizada.

> [!div class="nextstepaction"]
> [Criei um contêiner de armazenamento](tutorial-vscode-static-website-node-04.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-storage)
