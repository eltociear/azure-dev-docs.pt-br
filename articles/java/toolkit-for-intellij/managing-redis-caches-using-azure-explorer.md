---
title: Gerenciar Caches Redis utilizando o Azure Explorer para IntelliJ
description: Saiba como gerenciar seus Caches Redis do Azure utilizando o Azure Explorer para IntelliJ.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 2a059fa72207e8dde47eda7020ced90bd56b75a4
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81670152"
---
# <a name="managing-redis-caches-using-the-azure-explorer-for-intellij"></a>Gerenciar Caches Redis utilizando o Azure Explorer para IntelliJ

O Azure Explorer, que faz parte do Kit de ferramentas do Azure para IntelliJ, fornece aos desenvolvedores de Java com uma solução fácil de usar para gerenciar Caches Redis em sua conta do Azure de dentro do IDE IntelliJ.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-redis-cache-by-using-intellij"></a>Criar um Cache Redis utilizando IntelliJ

As etapas a seguir guiarão você pelas etapas para criar um Cache Redis utilizando o Azure Explorer.

1. Entre em sua conta do Azure usando as etapas no artigo [Instruções de entrada para o Kit de ferramentas do Azure para IntelliJ].

1. Na janela de ferramentas do **Azure Explorer**expanda o nó do **Azure**, clique com o botão direito do mouse em **Caches Redis** e, em seguida, clique em **Criar Cache Redis**.

   ![Criar menu Cache Redis][CR01]

1. Quando a caixa de diálogo **Novo Cache Redis** aparecer, especifique as seguinte opções:

   ![Criar uma caixa de diálogo Novo Cache Redis][CR02]

   a. **Nome DNS**: Especifica o subdomínio DNS para o novo cache redis, que são pré-anexados para ".redis.cache.windows.net", por exemplo: *wingtiptoys.redis.cache.windows.net*.

   b. **Assinatura**: especifica a assinatura do Azure que será utilizada para o novo Cache Redis.

   c. **Grupo de Recursos**: especifica o grupo de recursos para seu Cache Redis; é necessário escolher uma das seguintes opções: 
      * **Criar novo**: especifica que você deseja criar um novo grupo de recursos. 
      * **Usar existente**: especifica que você escolherá entre uma lista dos grupos de recursos associados à sua conta do Azure. 

   d. **Localização**: especifica a localização em que seu cache redis será criado; por exemplo, *Oeste dos EUA*.

   e. **Tipo de preço**: especifica qual tipo de preço seu Cache Redis utiliza; é a configuração que determina o número de conexões do cliente. (Para saber mais, veja [Preço do Cache Redis]).

   f. **Porta não SSL**: especifica se o Cache Redis permite conexões não SSL; por padrão, apenas conexões SSL são permitidas.

1. Quando tiver especificado todas as configurações de cache redis, clique em **OK**.

Depois que o cache redis tiver sido criado, ele será exibido no Azure Explorer.

   ![Cache Redis no Azure Explorer][CR03]

> [!NOTE]
>
> Para saber mais sobre como definir as configurações do seu cache redis do Azure, veja [Como configurar o Cache Redis do Azure].
>

## <a name="display-the-properties-for-your-redis-cache-in-intellij"></a>Exibir as propriedades para o Cache Redis em IntelliJ

1. No Azure Explorer, clique com o botão direito do mouse no cache redis e clique em **Mostrar propriedades**.

   ![Menu de contexto do Azure Explorer para exibir propriedades para um cache redis][SP01]

1. O Azure Explorer exibe as propriedades para o cache redis.

   ![Propriedades de cache Redis][SP02]

## <a name="delete-your-redis-cache-by-using-intellij"></a>Exclua o Cache Redis utilizando o IntelliJ

1. No Azure Explorer, clique com o botão direito do mouse no cache redis e clique em **Excluir**.

   ![Menu de contexto do Azure Explorer para excluir um cache redis][DE01]

1. Clique em **Sim** quando solicitado a excluir o cache redis.

   ![Excluir prompt do cache redis][DE02]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre caches redis, configurações e preços do Azure, veja os seguintes links:

* [Cache Redis do Azure]
* [Documentação do Cache Redis]
* [Preço do Cache Redis]
* [Como configurar o Cache Redis do Azure]

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Preço do Cache Redis]: https://azure.microsoft.com/pricing/details/cache/
[Cache Redis do Azure]: https://azure.microsoft.com/services/cache/
[Documentação do Cache Redis]: /azure/redis-cache
[Como configurar o Cache Redis do Azure]: /azure/redis-cache/cache-configure
[Instruções de entrada para o Kit de ferramentas do Azure para IntelliJ]: ./sign-in-instructions.md

<!-- IMG List -->

[CR01]: media/managing-redis-caches-using-azure-explorer/CR01.png
[CR02]: media/managing-redis-caches-using-azure-explorer/CR02.png
[CR03]: media/managing-redis-caches-using-azure-explorer/CR03.png

[SP01]: media/managing-redis-caches-using-azure-explorer/SP01.png
[SP02]: media/managing-redis-caches-using-azure-explorer/SP02.png

[DE01]: media/managing-redis-caches-using-azure-explorer/DE01.png
[DE02]: media/managing-redis-caches-using-azure-explorer/DE02.png
