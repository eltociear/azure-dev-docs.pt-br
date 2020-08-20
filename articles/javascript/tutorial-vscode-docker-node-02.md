---
title: Usar um Registro de contêiner do Visual Studio Code
description: Parte 2 do tutorial, usar um Registro de contêiner
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: c0eaa345af7d439cda47672f597ce1d82e78a355
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218423"
---
# <a name="use-a-container-registry"></a>Usar um registro de contêiner

[Etapa anterior: Introdução e pré-requisitos](tutorial-vscode-docker-node-01.md)

Nesta etapa, você configura um Registro de contêiner adequado para a imagem do aplicativo. Os serviços de hospedagem compatíveis com contêiner, como o Serviço de Aplicativo do Azure, efetuam pull das imagens do Registro.

Este tutorial usa o [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/), um Registro privado, seguro e hospedado para suas imagens. No entanto, as ferramentas e os processos mostrados aqui também funcionam com outros Registros, como o [Docker Hub](https://hub.docker.com/).

## <a name="create-an-azure-container-registry"></a>Criar um registro de contêiner do Azure

1. No Visual Studio Code, pressione **F1** para abrir a paleta de comandos.

1. Digite **Registro** na caixa de pesquisa. Nos resultados, selecione **Registro de Contêiner do Azure: Criar Registro**.

   ![O gerenciador do Docker no Visual Studio Code](media/deploy-containers/docker-create-registry.jpg)

1. Digite ou selecione os valores a seguir:

    - Em **Nome do Registro**, insira um nome que seja exclusivo no Azure e contenha de 5 a 50 caracteres alfanuméricos.
    - No **SKU**, selecione **Básico**.
    - Em **Grupo de recursos**, insira um valor que seja exclusivo em sua assinatura.
    - Em **Localização**, selecione uma região próxima de você.

    O Visual Studio Code começará o processo de criação do Registro no Azure. Após a conclusão, você verá uma notificação como a mostrada a seguir. Essa notificação confirma que o Registro foi criado com êxito.

   ![Uma confirmação de que o Registro foi criado no Visual Studio Code](media/deploy-containers/registry-created.jpg)

1. Abra o gerenciador do **Docker**. Verifique se o ponto de extremidade do Registro que você acabou de configurar está visível em **Registros**.

   ![Verificação de que o Registro aparece no gerenciador do Docker](media/deploy-containers/docker-explorer-registry.jpg)

## <a name="sign-in-to-azure-container-registry"></a>Entrar no Registro de Contêiner do Azure

Embora você possa ver seus Registros do Azure na extensão do Docker, não será possível enviar imagens por push para eles até que você entre no Registro de Contêiner.

1. No Visual Studio, selecione **CTRL**+ **`** para abrir o terminal integrado.

1. Execute o comando da CLI do Azure a seguir para entrar no Registro de Contêiner. Substitua `<your-registry-name>` pelo nome do Registro criado.

    ```bash
    az acr login --name <your-registry-name>
    ```

> [!div class="nextstepaction"]
> [Criei um Registro](tutorial-vscode-docker-node-03.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=create-registry)
 