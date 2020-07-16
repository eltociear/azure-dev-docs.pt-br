---
title: Exibir o conteúdo do Javadoc no Eclipse
titleSuffix: Azure Libraries for Java
description: Como exibir o conteúdo do Javadoc para as Bibliotecas do Azure no Eclipse.
documentationcenter: java
ms.assetid: 30f8b6a1-1d76-4d1c-861b-1db478c46e6b
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: cada923ab2c583d3c1b94c6397c09ea1ad760ac9
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378230"
---
# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Exibindo o Conteúdo do Javadoc no Eclipse para o Pacote de Bibliotecas do Azure para Java

O conteúdo do Javadoc para as bibliotecas do Azure para Java pode ser exibido em seu ambiente do Eclipse associando o conteúdo do Javadoc às bibliotecas do Azure para Java. As etapas a seguir mostram como usar essa funcionalidade no Eclipse.

Este procedimento pressupõe que você já adicionou a Biblioteca do Azure para Java ao seu caminho de compilação.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Para exibir o conteúdo do Javadoc no Eclipse para as Bibliotecas do Azure para Java

1. No Explorador de projeto do Eclipse, na seção **Bibliotecas Referenciadas** do seu projeto, abra o menu de contexto para a Biblioteca do Azure para Java JAR. Por exemplo, **microsoft-windowsazure-api-0.1.0.jar** (o número de versão pode ser diferente, dependendo de qual versão você instalou).

1. Clique em **Propriedades**.

1. Na caixa de diálogo **Propriedades**, no painel à esquerda, cliquem em **Javadoc Location**. A caixa de diálogo **Javadoc local** será exibida.

1. Você pode especificar uma **URL Javadoc**, ou um **Javadoc no arquivo**.

   * Se você optar por especificar uma **URL do Javadoc**, use as URLs como `https://dl.windowsazure.com/javadoc` ou `https://dl.windowsazure.com/storage/javadoc`.

   * Se optar por usar **Javadoc no arquivo**, você pode especificar um arquivo externo ou um arquivo de workspace.

   Faça sua escolha e procure/valide conforme necessário. O exemplo a seguir associa as Bibliotecas do Azure para Java com o Javadoc JAR correspondente que foi baixado localmente para uma pasta chamada **c:\MyAzureJARs**.

   ![Exiba o conteúdo do Javadoc.][ic553487]

1. *Etapa opcional*: Clique em **Validar**. Problemas potenciais com o Javadoc JAR podem ser exibidos aqui.

1. Clique em **OK**.

Quando associado à biblioteca, o conteúdo do Javadoc deve exibir seu IDE do Eclipse. Por exemplo, se `blob` definido do tipo `CloudBlockBlob` dentro de seu código, o seguinte é um exemplo do conteúdo do Javadoc que aparece quando você digita `blob.acquireLease` no código:

![Exiba o conteúdo do Javadoc.][ic553488]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

<!-- IMG List -->

[ic553487]: media/displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: media/displaying-javadoc-content-for-azure-libraries/ic553488.png
