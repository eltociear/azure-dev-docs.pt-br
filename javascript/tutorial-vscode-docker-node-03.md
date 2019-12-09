---
title: Criar uma imagem de contêiner para um aplicativo Node.js usando o Visual Studio Code
description: Parte 3 do tutorial, criar uma imagem de aplicativo Node.js
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: c3662c7d21359008bdc0cc5c3050fb2fdc7d6241
ms.sourcegitcommit: 9d0a6de18d9b5180c1cb485eff8e2774de48d225
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74540513"
---
# <a name="create-your-nodejs-application-image"></a>Criar a imagem do aplicativo Node.js

[Etapa anterior: Usar um Registro de contêiner](tutorial-vscode-docker-node-02.md)

Nesta etapa, você usa a extensão do Docker no Visual Studio Code para adicionar os arquivos necessários para criar uma imagem para seu aplicativo, criar a imagem e enviá-la por push a um Registro.

Se você ainda não tiver um aplicativo para este passo a passo, use o aplicativo no [tutorial do Node.js do Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="add-docker-files"></a>Adicionar arquivos do Docker

1. No Visual Studio Code, abra a **Paleta de Comandos** (**F1**), digite `add docker files to workspace` e selecione o comando **Docker: Adicionar arquivos do Docker ao workspace**.

1. Quando solicitado, selecione **Node.js** como o tipo de aplicativo e, em seguida, selecione a porta que o aplicativo escuta.

1. O comando cria um `Dockerfile` junto com alguns arquivos de configuração para o Docker Compose e um `.dockerignore`.

    > [!TIP]
    > O VS Code tem excelente suporte para arquivos do Docker. Confira [Como trabalhar com o Docker](https://code.visualstudio.com/docs/azure/docker) na documentação do VS Code para saber mais sobre recursos de linguagem avançados, como sugestões inteligentes, preenchimentos e detecção de erros.

## <a name="build-a-docker-image"></a>Compilar uma imagem do docker

O `Dockerfile` descreve o ambiente para seu aplicativo, incluindo o local dos arquivos de origem e o comando para iniciar o aplicativo dentro de um contêiner.

> [!TIP]
> Contêineres versus imagens: Um contêiner é uma instância de uma imagem.

1. Abra a **Paleta de Comandos** (**F1**) e execute **Imagens do Docker: Compilar Imagem** para compilar a imagem.

1. Quando solicitado, escolha o `Dockerfile` que acaba de ser criado e dê um nome à imagem. O nome deve incluir o Registro de destino ou a conta do Docker Hub:

    `[registry or username]/[image name]:[tag]`

    Neste tutorial, que usa o Registro de Contêiner do Azure, o nome da imagem é o seguinte:

    `msdocsvscodereg.azurecr.io/myexpressapp:latest`

    Se você estiver usando o Docker Hub, use o nome de usuário do Docker Hub. Por exemplo:

    `fiveisprime/myexpressapp:latest`

1. Depois de concluído, o painel **Terminal** do Visual Studio Code é aberto para executar o comando `docker build`. A saída também mostra cada etapa, ou camada, que compõe o ambiente de aplicativo.

1. Uma vez compilada, a imagem aparece no gerenciador do **DOCKER** em **Images**.

    ![Lista de imagens do Docker no Visual Studio Code](media/deploy-containers/image-list.png)

## <a name="push-the-image-to-a-registry"></a>Efetue o push da imagem para um Registro

1. No Visual Studio Code, abra a **Paleta de Comandos** (**F1**) e execute **Imagens do Docker: enviar por push** e escolha a imagem que você acabou de criar. O painel **Terminal** mostra os comandos `docker push` usados para esta operação.

1. Depois de concluído, expanda o nó **Imagens** no gerenciador de extensões do Docker para ver sua imagem.

    ![Imagem enviada por push exibida no Registro de Contêiner do Azure](media/deploy-containers/image-in-acr.png)

> [!div class="nextstepaction"]
> [Criei uma imagem para meu aplicativo](tutorial-vscode-docker-node-04.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=containerize-app)
