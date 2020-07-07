---
title: Armazenamento em nuvem para criar aplicativos altamente seguros, duráveis e escalonáveis com o Armazenamento do Azure
description: Saiba mais sobre os serviços usados para armazenar dados grandes estruturados e não estruturados de aplicativos móveis na nuvem.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 7a1171ebe64b0353fc57180adce63ee702431207
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632358"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Armazenamento em nuvem para aplicativos altamente seguros, duráveis e escalonáveis com o Armazenamento do Azure

O [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) é a solução de armazenamento em nuvem da Microsoft para aplicativos modernos que oferece um repositório de objetos altamente escalonável para objetos de dados, um serviço de sistema de arquivos para a nuvem, um repositório de mensagens para mensagens confiáveis e um repositório NoSQL. O Armazenamento do Azure é:

- **Durável e altamente disponível:** A redundância garante a segurança dos seus dados no caso de falhas de hardware transitórias. Você também pode optar por replicar os dados em data centers ou regiões geográficas para obter mais proteção contra catástrofes locais ou desastres naturais. Os dados replicados dessa maneira permanecem altamente disponíveis no caso de uma interrupção inesperada.
- **Seguro:** Todos os dados gravados no Armazenamento do Azure são criptografados pelo serviço. O Armazenamento do Azure oferece um controle refinado sobre quem possui acesso aos seus dados.
- **Escalonável:** os serviços foram projetados para ser altamente escalonável e atender às necessidades de desempenho e armazenamento de dados dos aplicativos atuais.
- **Gerenciado:** o Azure cuida da manutenção de hardware, das atualizações e dos problemas críticos para você.
- **Acessível:** os dados são acessíveis em qualquer lugar do mundo via HTTP ou HTTPS. A Microsoft fornece bibliotecas de clientes em uma variedade de linguagens, como .NET, Java, Node.js, Python, PHP, Ruby e Go, além de uma API REST madura. Há suporte para scripts no Azure PowerShell ou na CLI do Azure. O portal do Azure e o Gerenciador de Armazenamento do Azure oferecem soluções visualmente fáceis para o trabalho com os seus dados.

Use os serviços a seguir para habilitar o armazenamento em nuvem nos seus aplicativos móveis.

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

O [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) é uma solução de armazenamento de objetos para a nuvem. O Armazenamento de Blobs é otimizado para armazenar grandes volumes de dados não estruturados que não seguem uma definição ou um modelo de dados específico, como texto ou binário. Ele dá suporte à variedade de linguagens usadas pelas bibliotecas de clientes. O Armazenamento de Blobs foi projetado para:

- Fornecer imagens ou documentos diretamente a um navegador.
- Armazenar arquivos para acesso distribuído.
- Transmitir vídeo e áudio.
- Fazer gravações em arquivos de log.
- Armazenar dados para backup e restauração, recuperação de desastre e arquivamento.
- Armazenar dados para análise por um serviço local ou hospedado no Azure.

### <a name="azure-blob-storage-references"></a>Referências do Armazenamento de Blobs do Azure

- [Azure portal](https://portal.azure.com)
- [Documentação do Armazenamento de Blobs do Azure](/azure/storage/blobs/storage-blobs-introduction)
- [Inícios Rápidos](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Amostras](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Armazenamento da tabela do Azure

O [Armazenamento de Tabelas do Azure](https://azure.microsoft.com/services/storage/tables/) é um serviço que armazena dados NoSQL estruturados na nuvem e fornece um repositório de chaves ou atributos com um design sem esquema. O Armazenamento de Tabelas do Microsoft Azure armazena grandes quantidades de dados estruturados. O serviço é um armazenamento de dados NoSQL que aceita chamadas autenticadas dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. O Armazenamento de Tabelas normalmente é usado para:

- Armazenar terabytes de dados estruturados com a capacidade de atender a aplicativos de escala na Web.
- Armazenar conjuntos de dados que não exigem junções complexas, chaves estrangeiras ou procedimentos armazenados e que podem ser desnormalizados para acesso rápido.
- Consultar dados rapidamente por meio de um índice clusterizado.
- Acesse os dados usando o protocolo OData e as consultas LINQ com as bibliotecas .NET dos Serviços de Dados do WCF (Windows Communication Foundation).

Use o Armazenamento de Tabelas para armazenar e consultar grandes conjuntos de dados estruturados e não relacionais. As tabelas são dimensionadas conforme a demanda aumenta.

### <a name="azure-table-storage-references"></a>Referências do Armazenamento de Tabelas do Azure

- [Azure portal](https://portal.azure.com)
- [Documentação do Armazenamento de Tabelas do Azure](/azure/storage/tables/table-storage-overview)
- [Amostras](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Inícios Rápidos](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-queue-storage"></a>Armazenamento de Filas do Azure

O [armazenamento de Filas do Azure](https://azure.microsoft.com/services/storage/queues/) é um serviço usado para armazenar grandes volumes de mensagens. Acesse as mensagens em qualquer lugar do mundo por meio de chamadas autenticadas via HTTP ou HTTPS. Uma mensagem da fila pode ter até 64 KB. Uma fila pode conter milhões de mensagens, até o limite da capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processamento assíncrono.

###  <a name="azure-queue-storage-references"></a>Referência do armazenamento de Filas do Azure

- [Azure portal](https://portal.azure.com)
- [Documentação do armazenamento de Filas do Azure](/azure/storage/queues/)
- [Inícios Rápidos](/azure/storage/queues/storage-quickstart-queues-portal)
- [Amostras](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
