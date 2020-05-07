---
author: yevster
ms.author: yebronsh
ms.date: 1/20/2020
ms.openlocfilehash: 478059f598b9c39609a85647945ebef3df3cb7ec
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81671632"
---
### <a name="inventory-persistence-usage"></a>Uso da persistência de inventário

Qualquer uso do sistema de arquivos no servidor de aplicativos exigirá reconfiguração ou, em casos raros, alterações de arquitetura. Você pode identificar alguns ou todos os cenários a seguir.

#### <a name="read-only-static-content"></a>Conteúdo estático somente leitura

Se seu aplicativo atualmente estiver servindo conteúdo estático (por exemplo, por meio de uma integração do Apache), você precisará de um local alternativo para esse conteúdo estático. Talvez você queira considerar a movimentação de conteúdo estático para o Armazenamento de Blobs do Azure e a adição da CDN do Azure para downloads extremamente rápidos, globalmente. Para obter mais informações, confira [Hospedagem de site estático no Armazenamento do Microsoft Azure](/azure/storage/blobs/storage-blob-static-website) e [Início rápido: Integrar uma conta de armazenamento do Azure à CDN do Azure](/azure/cdn/cdn-create-a-storage-account-with-cdn#enable-azure-cdn-for-the-storage-account).

#### <a name="dynamically-published-static-content"></a>Conteúdo estático publicado dinamicamente

Se o aplicativo permitir conteúdo estático que é carregado/produzido pelo aplicativo, mas não puder ser alterado após sua própria criação, você poderá usar o Armazenamento de Blobs do Azure e a CDN do Azure, conforme descrito acima, com uma função do Azure para lidar com uploads e atualização de CDN. Fornecemos uma implementação de exemplo para seu uso em [Carregar conteúdo estático e fazer o pré-carregamento desse conteúdo pela CDN com o Azure Functions](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).
