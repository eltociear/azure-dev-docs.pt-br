---
title: Criar uma imagem de contêiner para um aplicativo Node.js usando o Visual Studio Code
description: Parte 4 do tutorial, criar uma imagem de aplicativo Node.js
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: 023f5fcba7231a6f767e3e61f6f212ddc2c385c0
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218431"
---
# <a name="create-your-nodejs-application-image"></a>Criar a imagem do aplicativo Node.js

[Etapa anterior: Criar e executar um aplicativo Node.js local](tutorial-vscode-docker-node-03.md)

## <a name="add-docker-files"></a>Adicionar arquivos do Docker

1. No Visual Studio Code, abra a **Paleta de Comandos** (**F1**), digite `add docker files to workspace` e selecione o comando **Docker: Adicionar arquivos do Docker ao workspace**.

1. Quando for solicitado, selecione **Node.js** para o tipo de aplicativo, responda **Não** para arquivos do Docker Compose e, em seguida, selecione a porta na qual seu aplicativo escutará (deve ser 3000).

1. O comando cria um `Dockerfile` junto com alguns arquivos de configuração para o Docker Compose e um `.dockerignore`.

    > [!TIP]
    > O VS Code tem excelente suporte para arquivos do Docker. Confira [Como trabalhar com o Docker](https://code.visualstudio.com/docs/azure/docker) na documentação do VS Code para saber mais sobre recursos de linguagem avançados, como sugestões inteligentes, preenchimentos e detecção de erros.

## <a name="build-a-docker-image"></a>Compilar uma imagem do docker

O `Dockerfile` descreve o ambiente para seu aplicativo, incluindo o local dos arquivos de origem e o comando para iniciar o aplicativo dentro de um contêiner.

> [!TIP]
> Contêineres versus imagens: Um contêiner é uma instância de uma imagem.

1. Abra a **Paleta de Comandos** (**F1**) e execute **Imagens do Docker: Compilar Imagem** para compilar a imagem. O VS Code usa o Dockerfile na pasta atual e dá à imagem o mesmo nome da pasta atual.

1. Depois de concluído, o painel **Terminal** do Visual Studio Code é aberto para executar o comando `docker build`. A saída também mostra cada etapa, ou camada, que compõe o ambiente de aplicativo.

1. Uma vez compilada, a imagem aparece no gerenciador do **DOCKER** em **Images**.

    ![Lista de imagens do Docker no Visual Studio Code](media/deploy-containers/image-list.png)

## <a name="push-the-image-to-a-registry"></a>Efetue o push da imagem para um Registro

1. Para enviar a imagem por push a um registro, você deverá marcá-la primeiro com o nome do registro. No gerenciador do **DOCKER**, clique com o botão direito do mouse na imagem **mais recente**.

    ![Comando de marca de imagem no Visual Studio Code](media/deploy-containers/tag-command.png)

1. No prompt a seguir, preencha as marcas e pressione **Inserir**.

    Por convenção, a marcação usa o seguinte formato:

    `[registry or username]/[image name]:[tag]`

    Caso esteja usando o Registro de Contêiner do Azure, o nome da imagem será semelhante ao seguinte:

    `msdocsvscodereg.azurecr.io/myexpressapp:latest`

    Se você estiver usando o Docker Hub, use o nome de usuário do Docker Hub. Por exemplo: 

    `fiveisprime/myexpressapp:latest`

1. A imagem recém-marcada aparece agora em um nó em **Imagens** que inclui o nome do registro. Expanda esse nó, clique com o botão direito do mouse no **mais recente** e selecione **Enviar por push**.

    ![Comando Enviar imagem por push no Visual Studio Code](media/deploy-containers/push-command.png)

1. O painel **Terminal** mostra os comandos `docker push` usados para esta operação. O registro de destino é determinado pelo registro especificado no nome da imagem.

   > [!IMPORTANT]
   > Se a saída exibir "Autenticação necessária", execute `az acr login --name <your registry name>` no terminal.

1. Depois de concluído, expanda o nó **Registros** no gerenciador de extensões do Docker para ver sua imagem no registro.

    ![Imagem enviada por push exibida no Registro de Contêiner do Azure](media/deploy-containers/image-in-acr.png)

> [!div class="nextstepaction"]
> [Criei uma imagem para o meu aplicativo](tutorial-vscode-docker-node-05.md) [Tive um problema](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=containerize-app)
