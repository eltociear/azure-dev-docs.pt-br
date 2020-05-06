---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 029277ac75c455041d26f160b1f0c16a5675368d
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673602"
---
### <a name="validate-whether-and-how-the-file-system-is-used"></a>Validar se e como o sistema de arquivos é usado

Os sistemas de arquivos de VMs funcionam da mesma forma que os sistemas de arquivos locais em relação à persistência, inicialização e ao desligamento. Mesmo assim, é importante estar ciente das necessidades do seu sistema de arquivos e garantir que as VMs tenham o tamanho e o desempenho de armazenamento adequados.

#### <a name="read-only-static-content"></a>Conteúdo estático somente leitura

Se seu aplicativo estiver servindo conteúdo estático no momento, você precisará de um local alternativo para ele. Talvez você queira considerar a movimentação de conteúdo estático para o Armazenamento de Blobs do Azure e a adição da CDN do Azure para downloads extremamente rápidos, globalmente. Para obter mais informações, confira [Hospedagem de site estático no Armazenamento do Microsoft Azure](/azure/storage/blobs/storage-blob-static-website) e [Início rápido: Integrar uma conta de armazenamento do Azure à CDN do Azure](/azure/cdn/cdn-create-a-storage-account-with-cdn).

#### <a name="dynamically-published-static-content"></a>Conteúdo estático publicado dinamicamente

Se o aplicativo permitir conteúdo estático que é carregado/produzido pelo aplicativo, mas não puder ser alterado após sua própria criação, você poderá usar o Armazenamento de Blobs do Azure e a CDN do Azure, conforme descrito acima, com uma função do Azure para lidar com uploads e atualização de CDN. Fornecemos uma implementação de exemplo para seu uso em [Carregar conteúdo estático e fazer o pré-carregamento desse conteúdo pela CDN com o Azure Functions](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).
