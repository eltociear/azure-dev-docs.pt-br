---
title: Gerenciar máquinas virtuais com o Azure Explorer para Eclipse
description: Saiba como gerenciar suas máquinas virtuais do Azure usando o Azure Explorer para Eclipse.
documentationcenter: java
ms.date: 08/25/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: e457d4fe152f9fa5fa64bafaa4f49311e8ff4475
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831882"
---
# <a name="manage-virtual-machines-by-using-the-azure-explorer-for-eclipse"></a>Gerenciar máquinas virtuais usando o Azure Explorer para Eclipse

O Azure Explorer, que faz parte do Kit de ferramentas do Azure para Eclipse, fornece aos desenvolvedores de Java uma solução fácil de usar para gerenciar máquinas virtuais em sua conta do Azure de dentro do IDE (ambiente de desenvolvimento integrado) Eclipse.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Entre em sua conta do Azure usando as [Instruções de conexão para o Kit de ferramentas do Azure para Eclipse](./sign-in-instructions.md).

1. Na exibição do **Azure Explorer**, expanda o nó **Azure**, clique com o botão direito do mouse em **Máquinas Virtuais** e, em seguida, clique em **Criar VM**.

   :::image type="content" source="media/managing-virtual-machines-using-azure-explorer/CR01.png" alt-text="Opção Criar VM no Azure Explorer.":::

1. Na caixa de diálogo **Escolha uma Assinatura**, selecione sua assinatura e clique em **Avançar**.

1. Na janela **Selecionar uma Imagem de Máquina Virtual**, selecione seu **Local** (por exemplo, *Oeste dos EUA*). Você terá a opção de continuar com uma imagem recomendada ou selecionar uma imagem personalizada. Para este guia de início rápido, continuaremos com a imagem recomendada. 

   Se você optar por selecionar uma imagem personalizada, insira as seguintes informações:
   * **Editor**: especifica o editor que criou a imagem que você usará para criar sua máquina virtual (por exemplo, *Microsoft*).

   * **Oferta**: especifica qual oferta de máquina virtual do editor selecionado será usada (por exemplo *JDK*).

   * **Sku**: especifica qual SKU (unidade de manutenção de estoque) da oferta selecionada será usada (por exemplo, *JDK_8*).

   * **No. da versão**: especifica qual versão do SKU selecionado será usada.

1. Clique em **Próximo**.

1. Na janela **Configurações Básicas de Máquina Virtual**, insira as seguintes informações:

   * **Nome da máquina virtual**: especifica o nome para sua nova máquina virtual que deve começar com uma letra e conter somente letras, números e hifens.

   * **Size**: especifica o número de núcleos e a memória para alocar para sua máquina virtual.

   * **Nome de Usuário**: especifica a conta de administrador a ser criada para gerenciar sua máquina virtual.

   * **Senha**: especifica a senha para sua conta de administrador. Reinsira a senha na caixa **Confirmar** para validar a credencial.

1. Clique em **Próximo**.

1. Na janela **Recursos Associados**, insira as informações a seguir:
   * **Grupo de Recursos**: especifica o grupo de recursos para sua máquina virtual. Selecione uma das seguintes opções:
      * **Criar novo**: especifica que você deseja criar um novo grupo de recursos.
      * **Usar existente**: especifica que você quer selecionar um grupo de recursos que já está associado à sua conta do Azure.

   * **Conta de armazenamento**: especifica a conta de armazenamento que será usada para armazenar a máquina virtual. Você pode usar uma conta de armazenamento existente ou criar uma nova.

   * **Rede virtual** e **Subrede**: especifica a rede virtual e a sub-rede às quais sua máquina virtual se conectará. Use uma rede e sub-rede existentes ou crie uma nova rede e sub-rede. Se você selecionar **Criar nova**, a caixa de diálogo a seguir será exibida:

   * **Endereço IP público**: especifica um endereço IP externo para sua máquina virtual. Você pode optar por criar um novo endereço IP ou, se sua máquina virtual não tiver um endereço IP público, selecione **(Nenhum)** .

   * **Grupo de segurança de rede**: especifica um firewall de rede opcional para sua máquina virtual. Selecione um firewall existente ou, se sua máquina virtual não for usar um firewall de rede, selecione **(Nenhum)** .

   * **Conjunto de disponibilidade**: especifica um conjunto de disponibilidade opcional ao qual sua máquina virtual pode pertencer. Você pode selecionar um conjunto de disponibilidade existente, criar um novo conjunto de disponibilidade ou, se sua máquina virtual não for pertencer a um conjunto de disponibilidade, selecionar **(Nenhum)** .

10. Clique em **Concluir**.  

      > [!NOTE]
      > Você pode verificar o progresso da criação no canto inferior direito do workspace do Eclipse.

## <a name="restart-a-virtual-machine"></a>Reiniciar uma máquina virtual

Para reiniciar uma máquina virtual usando o Azure Explorer no Eclipse, faça o seguinte:

1. Na exibição do **Azure Explorer**, clique com o botão direito do mouse na máquina virtual e selecione **Reiniciar**.

1. Na janela de confirmação, clique em **OK**.

   ![A janela de confirmação de reinício da máquina virtual](media/managing-virtual-machines-using-azure-explorer/RE02.png)

## <a name="shut-down-a-virtual-machine"></a>Desligar uma máquina virtual

Para desligar uma máquina virtual em execução usando o Azure Explorer no Eclipse, faça o seguinte:

1. Na exibição do **Azure Explorer**, clique com o botão direito do mouse na máquina virtual e selecione **Desligar**.

1. Na janela de confirmação, clique em **OK**.

   ![A janela de confirmação de desligamento da máquina virtual](media/managing-virtual-machines-using-azure-explorer/SH02.png)

## <a name="delete-a-virtual-machine"></a>Excluir uma máquina virtual

Para excluir uma máquina virtual usando o Azure Explorer no Eclipse, faça o seguinte:

1. Na exibição do **Azure Explorer**, clique com o botão direito do mouse na máquina virtual e selecione **Excluir**.

1. Na janela de confirmação, clique em **Sim**.

   ![A janela de confirmação de exclusão da máquina virtual](media/managing-virtual-machines-using-azure-explorer/DE02.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os tamanhos e preços das máquinas virtuais do Azure, veja os recursos a seguir:

* Tamanhos de máquinas virtuais do Azure
  * [Tamanhos das máquinas virtuais do Windows no Azure]
  * [Tamanhos das máquinas virtuais do Linux no Azure]
* Preços de máquinas virtuais do Azure
  * [Preços de máquinas virtuais do Windows]
  * [Preços de máquinas virtuais do Linux]

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Tamanhos das máquinas virtuais do Windows no Azure]: /azure/virtual-machines/sizes
[Tamanhos das máquinas virtuais do Linux no Azure]: /azure/virtual-machines/sizes
[Preços de máquinas virtuais do Windows]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[Preços de máquinas virtuais do Linux]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/

<!-- IMG List -->

[RE01]: media/managing-virtual-machines-using-azure-explorer/RE01.png
[RE02]: media/managing-virtual-machines-using-azure-explorer/RE02.png

[SH01]: media/managing-virtual-machines-using-azure-explorer/SH01.png
[SH02]: media/managing-virtual-machines-using-azure-explorer/SH02.png

[DE01]: media/managing-virtual-machines-using-azure-explorer/DE01.png
[DE02]: media/managing-virtual-machines-using-azure-explorer/DE02.png

[CR01]: media/managing-virtual-machines-using-azure-explorer/CR01.png
[CR02]: media/managing-virtual-machines-using-azure-explorer/CR02.png
[CR03]: media/managing-virtual-machines-using-azure-explorer/CR03.png
[CR04]: media/managing-virtual-machines-using-azure-explorer/CR04.png
[CR05]: media/managing-virtual-machines-using-azure-explorer/CR05.png
[CR06]: media/managing-virtual-machines-using-azure-explorer/CR06.png
[CR07]: media/managing-virtual-machines-using-azure-explorer/CR07.png
[CR08]: media/managing-virtual-machines-using-azure-explorer/CR08.png