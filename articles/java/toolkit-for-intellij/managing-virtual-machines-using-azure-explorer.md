---
title: Gerenciar máquinas virtuais com o Azure Explorer para IntelliJ
description: Saiba como gerenciar suas máquinas virtuais do Azure usando o Azure Explorer para IntelliJ.
documentationcenter: java
ms.date: 09/09/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 43aa5efc8a783887c4af0e62f2da7d3b51f652fe
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831923"
---
# <a name="manage-virtual-machines-by-using-the-azure-explorer-for-intellij"></a>Gerenciar máquinas virtuais usando o Azure Explorer para IntelliJ

O Azure Explorer, que faz parte do Kit de ferramentas do Azure para IntelliJ, fornece aos desenvolvedores de Java com uma solução fácil de usar para gerenciar máquinas virtuais em sua conta do Azure de dentro do IDE (ambiente de desenvolvimento integrado) IntelliJ.

Este artigo demonstra como criar e gerenciar máquinas virtuais usando o Azure Explorer em IntelliJ.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para criar uma máquina virtual usando o Azure Explorer, faça o seguinte: 

1. Entre em sua conta do Azure usando as etapas no artigo [Instruções de conexão para o Kit de ferramentas do Azure para IntelliJ].

2. Na exibição do **Azure Explorer**, expanda o nó **Azure**, clique com o botão direito do mouse em **Máquinas Virtuais** e, em seguida, clique em **Criar VM**. 
 
   :::image type="content" source="media/managing-virtual-machines-using-azure-explorer/CR01.png" alt-text="Opção Criar VM no Azure Explorer.":::

3. Na caixa de diálogo **Escolha uma Assinatura**, selecione sua assinatura e clique em **Avançar**. 

4. Na janela **Selecionar uma Imagem de Máquina Virtual**, insira as seguintes informações:

   * **Localização**: especifica a localização onde sua máquina virtual será criada (por exemplo, *oeste dos EUA*). 

   * **Imagem recomendada**: especifica que você escolherá uma imagem em uma lista abreviada de imagens usadas com frequência.

   * **Imagem personalizada**: especifica que você escolherá uma imagem personalizada fornecendo as seguintes informações:

      * **Editor**: especifica o editor que criou a imagem que você usará para sua máquina virtual (por exemplo, *Microsoft*).

      * **Oferta**: especifica qual oferta de máquina virtual do editor selecionado será usada (por exemplo *JDK*).

      * **Sku**: especifica qual SKU (unidade de manutenção de estoque) da oferta selecionada será usada (por exemplo, *JDK_8*).

      * **No. da versão**: especifica qual versão do SKU selecionado será usada.

5. Clique em **Próximo**. 

6. Na janela **Configurações Básicas de Máquina Virtual**, insira as seguintes informações:

   * **Nome da máquina virtual**: especifica o nome para sua nova máquina virtual que deve começar com uma letra e conter somente letras, números e hifens.

   * **Size**: especifica o número de núcleos e a memória para alocar para sua máquina virtual.

   * **Nome de Usuário**: especifica a conta de administrador a ser criada para gerenciar sua máquina virtual.

   * **Senha**: especifica a senha para sua conta de administrador. Reinsira a senha na caixa **Confirmar** para validar a credencial.

7. Clique em **Próximo**. 

8. Na janela **Recursos Associados**, insira as informações a seguir:

   * **Grupo de recursos**: especifica o grupo de recursos para sua máquina virtual. Selecione uma das seguintes opções:
      * **Criar novo**: especifica que você deseja criar um novo grupo de recursos.
      * **Usar existente**: especifica que você quer selecionar em uma lista de grupos de recursos associados à sua conta do Azure.

   * **Conta de armazenamento**: especifica a conta de armazenamento que será usada para armazenar a máquina virtual. Escolha uma conta de armazenamento existente ou crie uma nova. Se você escolher **Criar Nova**, a caixa de diálogo a seguir será exibida:

   * **Rede virtual** e **Subrede**: especifica a rede virtual e a sub-rede às quais sua máquina virtual se conectará. Use uma rede e sub-rede existentes ou crie uma nova rede e sub-rede. Se você selecionar **Criar novo**, a caixa de diálogo a seguir será exibida:

   * **Endereço IP público**: especifica um endereço IP externo para sua máquina virtual. Você pode optar por criar um novo endereço IP ou, se sua máquina virtual não tiver um endereço IP público, selecione **(Nenhum)** . 

   * **Grupo de segurança de rede**: especifica um firewall de rede opcional para sua máquina virtual. Selecione um firewall existente ou, se sua máquina virtual não for usar um firewall de rede, selecione **(Nenhum)** . 

   * **Conjunto de disponibilidade**: especifica um conjunto de disponibilidade opcional ao qual sua máquina virtual pode pertencer. Selecione um conjunto de disponibilidade existente, crie um novo conjunto de disponibilidade ou, se sua máquina virtual não for pertencer a um conjunto de disponibilidade, selecione **(Nenhum)** .

9. Clique em **Concluir**. Sua nova máquina virtual aparece na janela de ferramentas do Azure Explorer. 

## <a name="restart-a-virtual-machine"></a>Reiniciar uma máquina virtual

Para reiniciar uma máquina virtual usando o Azure Explorer no IntelliJ, faça o seguinte:

1. Na exibição do **Azure Explorer**, clique com o botão direito do mouse na máquina virtual e selecione **Reiniciar**.

2. Na janela de confirmação, clique em **Sim**. 

   ![A janela de confirmação de reinicialização da máquina virtual][RE02]

## <a name="shut-down-a-virtual-machine"></a>Desligar uma máquina virtual

Para desligar uma máquina virtual em execução usando o Azure Explorer no IntelliJ, faça o seguinte:

1. Na exibição do **Azure Explorer**, clique com o botão direito do mouse na máquina virtual e selecione **Desligar**.

2. Na janela de confirmação, clique em **Sim**. 

   ![A janela de confirmação de desligamento da máquina virtual][SH02]

## <a name="delete-a-virtual-machine"></a>Excluir uma máquina virtual

Para excluir uma máquina virtual usando o Azure Explorer no IntelliJ, faça o seguinte:

1. Na exibição do **Azure Explorer**, clique com o botão direito do mouse na máquina virtual e selecione **Excluir**.

2. Na janela de confirmação, clique em **Sim**. 

   ![A janela de confirmação de exclusão da máquina virtual][DE02]

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

[Instruções de conexão para o Kit de ferramentas do Azure para IntelliJ]: ./sign-in-instructions.md
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