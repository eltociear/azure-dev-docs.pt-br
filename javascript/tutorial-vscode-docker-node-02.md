---
title: Usar um Registro de contêiner do Visual Studio Code
description: Parte 2 do tutorial, usar um Registro de contêiner
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: e6dde135a2e6482284488fb83d9f811b02249c4d
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80740564"
---
# <a name="use-a-container-registry"></a>Usar um registro de contêiner

[Etapa anterior: Introdução e pré-requisitos](tutorial-vscode-docker-node-01.md)

Nesta etapa, você configura um Registro de contêiner adequado para a imagem do aplicativo. Os serviços de hospedagem compatíveis com contêiner como o Serviço de Aplicativo do Azure então efetuam pull das imagens do Registro.

Este tutorial usa o ACR [(Registro de Contêiner do Azure)](https://azure.microsoft.com/services/container-registry/), um registro privado, seguro e hospedado para suas imagens. As ferramentas e os processos mostrados aqui, no entanto, também funcionam com outros Registros, como o [Docker Hub](https://hub.docker.com/).

## <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure

1. No Visual Studio Code, pressione <kbd>F1</kbd> para abrir a **Paleta de Comandos**.

1. Digite "registro" na caixa de pesquisa e selecione **Registro de Contêiner do Azure: Criar Registro**.

   ![O gerenciador do Docker no VS Code](media/deploy-containers/docker-create-registry.jpg)

1. Forneça os seguintes valores nos prompts...

    - O **nome do Registro** deve ser exclusivo em todo o Azure e conter de 5 a 50 caracteres alfanuméricos.
    - Selecione **Básico** para **SKU**.
    - O **grupo de recursos** precisa ser exclusivo somente dentro de sua assinatura.
    - Em local, **Local**, selecionando uma região perto de você.

    O Visual Studio Code começará o processo de criação do registro no Azure. Ao concluir, você verá uma notificação como a abaixo, confirmando que o registro foi criado com êxito.

   ![Uma confirmação de que o registro foi criado no Visual Studio Code](media/deploy-containers/registry-created.jpg)

1. Abra o gerenciador do **Docker** e verifique se o ponto de extremidade do registro que você acabou de configurar está visível em **Registros**:

   ![Verificando se o Registro aparece no gerenciador do Docker](media/deploy-containers/docker-explorer-registry.jpg)

## <a name="sign-in-to-azure-container-registry"></a>Entrar no Registro de Contêiner do Azure

Embora você possa ver seus registros do Azure na extensão do Docker, não será possível enviar imagens por push para eles até que você faça logon no ACR (Registro de Contêiner do Azure).

1. Pressione <kbd>CTRL + `</kbd> para abrir o **Terminal Integrado** no VS Code.

1. Execute o seguinte comando da CLI do Azure para fazer logon no ACR. Substitua "<your-registry-name>" pelo nome do registro que você acabou de criar.

    ```bash
    az acr login --name <your-registry-name>
    ```

> [!div class="nextstepaction"]
> [Criei um registro](tutorial-vscode-docker-node-03.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=create-registry)
