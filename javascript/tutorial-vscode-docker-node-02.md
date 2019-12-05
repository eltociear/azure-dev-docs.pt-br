---
title: Usar um Registro de contêiner do Visual Studio Code
description: Parte 2 do tutorial, usar um Registro de contêiner
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 7d51e8011824ec19e9530f9bc94bcb2ce07f2851
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466634"
---
# <a name="use-a-container-registry"></a>Usar um registro de contêiner

[Etapa anterior: Introdução e pré-requisitos](tutorial-vscode-docker-node-01.md)

Nesta etapa, você configura um Registro de contêiner adequado para a imagem do aplicativo. Os serviços de hospedagem compatíveis com contêiner como o Serviço de Aplicativo do Azure então efetuam pull das imagens do Registro.

Este tutorial usa o ACR [(Registro de Contêiner do Azure)](https://azure.microsoft.com/services/container-registry/), um registro privado, seguro e hospedado para suas imagens. As ferramentas e os processos mostrados aqui, no entanto, também funcionam com outros Registros, como o [Docker Hub](https://hub.docker.com/).

## <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure

1. Entre no [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

    ![Criar um registro de contêiner no portal do Azure](media/deploy-containers/portal-01.png)

1. No formulário **Criar Registro de contêiner** que aparece, insira os valores apropriados:

    - O **nome do Registro** deve ser exclusivo em todo o Azure e conter de 5 a 50 caracteres alfanuméricos.
    - Selecione sua assinatura em **Assinatura**.
    - O **grupo de recursos** precisa ser exclusivo somente dentro de sua assinatura.
    - Em local, **Local**, selecionando uma região perto de você.
    - Defina o **usuário Administrador** como **Habilitar**.
    - Selecione **Básico** para **SKU**.

    ![Valores para o formulário de Registro de contêiner](media/deploy-containers/portal-02.png)

1. Selecione **Criar** para criar o Registro.

1. Depois que o Registro for criado, abra as notificações no portal e selecione **Ir para o Recurso** para o Registro:

    ![Como abrir o recurso de Registro recém-criado](media/deploy-containers/portal-03.png)

1. Na página de Registro, selecione **Chaves de Acesso** e anote as credenciais de administrador:

    ![Credenciais de Registro para o Registro no portal do Azure](media/deploy-containers/portal-04.png)

1. Em um prompt de comando ou terminal, faça logon no Docker usando o comando a seguir, substituindo `<registry_name>` pelo nome do Registro e `<username>` e `<password>` pelos valores mostrados no portal do Azure para o usuário administrador:

    ```bash
    docker login <registry_name>.azurecr.io -u <username> -p <password>
    ```

1. No Visual Studio Code, abra o gerenciador do **Docker** e verifique se o ponto de extremidade do Registro que você acabou de configurar está visível em **Registros**:

    ![Verificando se o Registro aparece no gerenciador do Docker](media/deploy-containers/registries.png)

> [!div class="nextstepaction"]
> [Criei um Registro](tutorial-vscode-docker-node-03.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=create-registry)
