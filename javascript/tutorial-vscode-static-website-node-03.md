---
title: Criar uma conta de Armazenamento do Azure para um site do Node.js estático usando o Visual Studio Code
description: Parte 3 do tutorial, criar uma conta de Armazenamento do Azure
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 42badfc649d7cc43eb1a58ab20c8ff639eff5354
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466498"
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

1. No prompt "Selecionar um grupo de recursos", selecione **Criar um Novo Grupo de Recursos** e aceite o nome padrão.

1. No prompt "Selecionar um local", escolha uma [região](https://azure.microsoft.com/regions/) perto de você.

1. Enquanto a conta de armazenamento é criada, o progresso aparece no painel **Saída** do VS Code:

1. Quando a conta de armazenamento for concluída, clique com o botão direito do mouse nela e selecione **Configurar o Site Estático**. Habilitar a hospedagem de site estático significa que o Armazenamento do Azure fornece automaticamente seu documento de índice e quaisquer outros ativos estáticos.

    ![Criar conta de armazenamento](media/static-website/configure-static-website.png)

1. Quando solicitado, insira *index.html* para o nome do documento de índice e o nome do documento de erro 404. Usamos *index.html* para o documento de erro porque os SPAs (aplicativos de página única) modernos, como React, Angular e Vue, tratam dos erros no cliente. Para sites estáticos clássicos, use uma página de erro 404 personalizada.

> [!div class="nextstepaction"]
> [Criei um contêiner de armazenamento](tutorial-vscode-static-website-node-04.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-storage)
