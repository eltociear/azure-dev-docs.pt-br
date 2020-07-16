---
title: Gerenciar contas de armazenamento com o Azure Explorer para Eclipse
description: Saiba como gerenciar suas contas de armazenamento do Azure usando o Azure Explorer para Eclipse.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 19f529b6d04e73c18648b77b2ac83a31ca1a455f
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378130"
---
# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-eclipse"></a>Gerenciar contas de armazenamento usando o Azure Explorer para Eclipse

O Azure Explorer, que faz parte do Kit de ferramentas do Azure para Eclipse, fornece aos desenvolvedores de Java com uma solução fácil de usar para gerenciar contas de armazenamento em sua conta do Azure de dentro do IDE (ambiente de desenvolvimento integrado) Eclipse.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-storage-account-in-eclipse"></a>Criar uma conta de armazenamento no Eclipse

Para criar uma conta de armazenamento usando o Azure Explorer, faça o seguinte:

1. Entre em sua conta do Azure usando as [Instruções de conexão para o Kit de ferramentas do Azure para Eclipse](/azure/developer/java/toolkit-for-eclipse/sign-in-instructions).

1. Na exibição do **Azure Explorer**, expanda o nó **Azure**, clique com o botão direito do mouse em **Contas de Armazenamento** e, em seguida, clique em **Criar Conta de Armazenamento**.

   ![Comando para Criar Conta de Armazenamento][CS01]

1. Na caixa de diálogo **Criar Conta de Armazenamento**, especifique as opções a seguir:

   ![Caixa de diálogo Criar Nova Conta de Armazenamento][CS02]

   * **Name**: especifica o nome que você deseja usar para a nova conta de armazenamento.

   * **Assinatura**: especifica a assinatura do Azure que você deseja usar para a nova conta de armazenamento.

   * **Grupo de Recursos**: especifica o grupo de recursos para sua máquina virtual. Selecione uma das seguintes opções:
      * **Criar novo**: especifica que você deseja criar um novo grupo de recursos.
      * **Usar existente**: especifica que você selecionará em uma lista de grupos de recursos associados à sua conta do Azure.

   * **Região**: especifica a localização em que sua conta de armazenamento será criada (por exemplo, "Oeste dos EUA").

   * **Tipo de conta**: especifica o tipo de conta de armazenamento a ser criada (por exemplo, "Armazenamento de Blobs"). Para saber mais, confira [Sobre as contas de armazenamento do Azure].

   * **Desempenho**: especifica qual oferta de conta de armazenamento usar do editor selecionado (por exemplo "Premium"). Para saber mais, veja [Metas de desempenho e escalabilidade do Armazenamento do Azure].

   * **Replicação**: especifica a replicação para a conta de armazenamento (por exemplo "Com Redundância de Zona"). Para saber mais, veja [Replicação do Armazenamento do Azure].

1. Quando você tiver especificado todas as opções anteriores, clique em **Criar**.

## <a name="create-a-storage-container-in-eclipse"></a>Criar um contêiner de armazenamento no Eclipse

Para criar um contêiner de armazenamento usando o Azure Explorer, faça o seguinte:

1. Na exibição do **Azure Explorer**, clique com o botão direito do mouse na conta de armazenamento em que deseja criar um contêiner e, em seguida, clique em **Criar contêiner de blob**.

   ![Comando Criar contêiner de blob][CC01]

1. Na caixa de diálogo **Criar Contêiner de Blob**, especifique o nome do seu contêiner e, em seguida, clique em **OK**. Para saber mais sobre como nomear contêineres de armazenamento, veja [Nomenclatura e referência de contêineres, blobs e metadados].

   ![Caixa de diálogo Criar contêiner de blob][CC02]

## <a name="delete-a-storage-container-in-eclipse"></a>Excluir um contêiner de armazenamento no Eclipse

Para excluir um contêiner de armazenamento usando o Azure Explorer, faça o seguinte:

1. Na exibição **Azure Explorer**, clique com o botão direito do mouse no contêiner de armazenamento e, em seguida, clique em **Excluir**.

   ![Comando Excluir contêiner de armazenamento][DC01]

1. Na janela de confirmação, clique em **OK**.

   ![Janela de confirmação de Excluir contêiner de armazenamento][DC02]

## <a name="delete-a-storage-account-in-eclipse"></a>Excluir uma conta de armazenamento no Eclipse

Para excluir uma conta de armazenamento usando o Azure Explorer, faça o seguinte:

1. Na exibição do **Azure Explorer**, clique com o botão direito do mouse na conta de armazenamento e clique em **Excluir**.

   ![Comando Excluir conta de armazenamento][DS01]

1. Na janela de confirmação, clique em **OK**.

   ![Janela de confirmação de Excluir conta de armazenamento][DS02]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os tamanhos, preços e contas de armazenamento do Azure, veja os recursos a seguir:

* [Introdução ao Armazenamento do Microsoft Azure]
* [Sobre as contas de armazenamento do Azure]
* Tamanhos de conta de armazenamento do Azure
  * [Tamanhos das contas de armazenamento do Windows no Azure]
  * [Tamanhos das contas de armazenamento do Linux no Azure]
* Preços da conta de armazenamento do Azure
  * [Preços da conta de armazenamento do Windows]
  * [Preços da conta de armazenamento do Linux]

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Introdução ao Armazenamento do Microsoft Azure]: /azure/storage/common/storage-introduction
[Sobre as contas de armazenamento do Azure]: /azure/storage/storage-create-storage-account
[Replicação do Armazenamento do Azure]: /azure/storage/storage-redundancy
[Metas de desempenho e escalabilidade do Armazenamento do Azure]: /azure/storage/storage-scalability-targets
[Nomenclatura e referência de contêineres, blobs e metadados]: https://go.microsoft.com/fwlink/?LinkId=255555

[Tamanhos das contas de armazenamento do Windows no Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Tamanhos das contas de armazenamento do Linux no Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Preços da conta de armazenamento do Windows]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[Preços da conta de armazenamento do Linux]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: media/managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: media/managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: media/managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: media/managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: media/managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: media/managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: media/managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: media/managing-storage-accounts-using-azure-explorer/DC02.png
