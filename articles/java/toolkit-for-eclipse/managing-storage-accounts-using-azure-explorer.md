---
title: Gerenciar contas de armazenamento com o Azure Explorer para Eclipse
description: Saiba como gerenciar suas contas de armazenamento do Azure usando o Azure Explorer para Eclipse.
documentationcenter: java
ms.date: 08/25/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: e5b5c946b5ed4d2480b1040d0925507453cc2408
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831742"
---
# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-eclipse"></a>Gerenciar contas de armazenamento usando o Azure Explorer para Eclipse

> [!NOTE]
> O recurso de Contas de Armazenamento no Azure Explorer foi preterido. Você pode utilizar o portal do Azure para criar e gerenciar contas de armazenamento e contêineres. Confira a documentação do [Armazenamento do Azure](/azure/storage/blobs/storage-quickstart-blobs-portal) para obter guias de início rápido sobre como gerenciar contas de armazenamento.

O Azure Explorer, que faz parte do Azure Toolkit for Eclipse, fornece aos desenvolvedores de Java com uma solução fácil de usar para gerenciar contas de armazenamento em sua conta do Azure de dentro do IDE (ambiente de desenvolvimento integrado) Eclipse.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Entre em sua conta do Azure usando as [Instruções de conexão para o Kit de ferramentas do Azure para Eclipse](./sign-in-instructions.md).

1. Na exibição do **Azure Explorer**, expanda o nó **Azure**, clique com o botão direito do mouse em **Contas de Armazenamento** e, em seguida, clique em **Criar Conta de Armazenamento**.

1. Na caixa de diálogo **Criar Conta de Armazenamento**, especifique as opções a seguir:

   * **Name**: especifica o nome que você deseja usar para a nova conta de armazenamento.

   * **Assinatura**: especifica a assinatura do Azure que você deseja usar para a nova conta de armazenamento.

   * **Grupo de Recursos**: especifica o grupo de recursos para sua máquina virtual. Selecione uma das seguintes opções:
      * **Criar novo**: especifica que você deseja criar um novo grupo de recursos.
      * **Usar existente**: especifica que você selecionará em uma lista de grupos de recursos associados à sua conta do Azure.

   * **Região**: especifica a localização em que sua conta de armazenamento será criada (por exemplo, "Oeste dos EUA").

   * **Tipo de conta**: especifica o tipo de conta de armazenamento a ser criada (por exemplo, "Uso geral v1"). Para saber mais, confira [Sobre as contas de armazenamento do Azure].

   * **Desempenho**: especifica qual oferta de conta de armazenamento usar do editor selecionado (por exemplo "Standard"). Para saber mais, veja [Metas de desempenho e escalabilidade do Armazenamento do Azure].

   * **Replicação**: especifica a replicação para a conta de armazenamento (por exemplo "Com Redundância Local"). Para saber mais, veja [Replicação do Armazenamento do Azure].

1. Quando você tiver especificado todas as opções anteriores, clique em **Criar**.

## <a name="create-and-manage-storage-containers"></a>Criar e gerenciar contêineres de armazenamento

Para criar e gerenciar contêineres de armazenamento, acesse o portal do Azure ou provisione seus recursos programaticamente.

Confira [Carregar, baixar e listar blobs com o portal do Azure](/azure/storage/blobs/storage-quickstart-blobs-portal) para obter um tutorial passo a passo de como usar o portal do Azure para criar um contêiner no armazenamento do Azure e carregar e baixar blobs de blocos nesse contêiner.

## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento

1. Na exibição do **Azure Explorer**, clique com o botão direito do mouse na conta de armazenamento e clique em **Excluir**.

1. Na janela de confirmação, clique em **OK**.


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
[Naming and referencing containers, blobs, and metadata]: https://go.microsoft.com/fwlink/?LinkId=255555

[Tamanhos das contas de armazenamento do Windows no Azure]: /azure/virtual-machines/sizes
[Tamanhos das contas de armazenamento do Linux no Azure]: /azure/virtual-machines/sizes
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