---
title: Obtenha as bibliotecas do Azure (SDK) para Python
description: Visão geral dos recursos e das funcionalidades das bibliotecas do Azure para Python que ajudam os desenvolvedores a serem mais produtivos ao provisionar, usar e gerenciar recursos do Azure.
ms.date: 05/26/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: dce4485a65da75d0918805ef55fafe71d55ce0a6
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983278"
---
# <a name="use-the-azure-libraries-sdk-for-python"></a>Obtenha as bibliotecas do Azure (SDK) para Python

As bibliotecas de software livre do Azure para Python simplificam o provisionamento, o gerenciamento e o uso dos recursos do Azure com base no código de aplicativo do Python.

## <a name="the-details-you-really-want-to-know"></a>Os detalhes que você realmente quer saber

- As bibliotecas do Azure são como você se comunica com os serviços do Azure *do* código Python executado localmente ou na nuvem. (A possibilidade de executar o código Python dentro do escopo de um serviço específico depende se esse serviço atualmente dá suporte a Python.)

- As bibliotecas são compatíveis com Python 2.7 e Python 3.5.3 ou posterior e foram testadas também com PyPy 5.4+.

- O SDK do Azure é composto por mais de 180 bibliotecas Python individuais relacionadas a serviços específicos do Azure. Não há outras ferramentas no "SDK".

- Ao executar o código localmente, a autenticação com o Azure depende de variáveis de ambiente, conforme descrito em [Configurar seu ambiente de desenvolvimento local](configure-local-development-environment.md). 

- Para instalar os pacotes de biblioteca de que precisa com `pip install <library_name>`, use os nomes de biblioteca no [índice de pacotes do SDK do Python](azure-sdk-library-package-index.md). Para obter mais detalhes, consulte [Instalar bibliotecas do Azure](azure-sdk-install.md).

- Há bibliotecas de "gerenciamento" e de "cliente" distintas (às vezes chamadas de bibliotecas de "plano de gerenciamento" e de "plano de dados"). Cada conjunto atende a diferentes finalidades e é usado por diferentes tipos de código. Para obter mais informações, consulte as seguintes seções deste artigo:
  - [Provisionar e gerenciar recursos do Azure com bibliotecas de gerenciamento](#provision-and-manage-azure-resources-with-management-libraries)
  - [Conectar e usar os recursos do Azure com bibliotecas de clientes](#connect-to-and-use-azure-resources-with-client-libraries)

- A documentação das bibliotecas é encontrada na [Referência do Azure para Python](/python/api/overview/azure/?view=azure-python), que é organizada por Serviço do Azure ou no [navegador de API do Python](/python/api/?view=azure-python), que é organizado por nome de pacote. No momento, pode ser necessário clicar em várias camadas para obter as classes e os métodos do seu interesse. Queremos nos desculpar por essa experiência insatisfatória. Estamos trabalhando para melhorá-la.

- Para experimentar as bibliotecas por conta própria, recomendamos primeiro [configurar seu ambiente de desenvolvimento local](configure-local-development-environment.md). Em seguida, você pode experimentar qualquer um dos seguintes exemplos independentes (em qualquer ordem): [Exemplo: Provisionar um grupo de recursos](azure-sdk-example-resource-group.md), [Exemplo: Provisionar e usar o Armazenamento do Microsoft Azure](azure-sdk-example-storage.md), [Exemplo: Provisionar um aplicativo Web e implantar o código](azure-sdk-example-web-app.md), [Exemplo: Provisionar e usar um banco de dados MySQL](azure-sdk-example-database.md) e [Exemplo: Provisionar uma máquina virtual](azure-sdk-example-virtual-machines.md).

- Para assistir a um vídeo de demonstração, confira <a href="https://www.youtube.com/watch?v=M1pVxItg2Mg&feature=youtu.be&ocid=AID3006292" target="_blank">Usar SDKs do Azure para interagir com o recurso do Azure</a> (youtube.com) do PyCon 2020 virtual.

### <a name="non-essential-but-still-interesting-details"></a>Informações não essenciais, mas ainda interessantes

- Como a CLI do Azure é escrita em Python usando as bibliotecas de gerenciamento, tudo que é possível fazer com os comandos da CLI do Azure também pode ser feito com um script Python. Dito isso, os comandos da CLI fornecem muitos recursos úteis, como a execução de várias tarefas ao mesmo tempo, o tratamento automático de operações assíncronas, a formatação de saída como cadeias de conexão e assim por diante. Consequentemente, usar a CLI (ou seu equivalente, o Azure PowerShell) para scripts automatizados de provisionamento e gerenciamento pode ser significativamente mais conveniente do que escrever o código Python equivalente, a menos que você queira ter um grau muito maior de controle sobre o processo.

- As bibliotecas da Azure para Python criam sobre a API REST subjacente do Azure, permitindo que você use essas APIs por meio de paradigmas conhecidos do Python. No entanto, você sempre pode usar a API REST diretamente do código Python, se desejar.

- Você pode encontrar o código-fonte para as bibliotecas do Azure em [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python). Como um projeto de software livre, contribuições são bem-vindas!

- Embora você possa usar as bibliotecas com interpretadores, como IronPython e Jython, com os quais não comparamos, é possível encontrar problemas isolados e incompatibilidades.

- O repositório de fonte para a documentação de referência da API de biblioteca reside em [https://github.com/MicrosoftDocs/azure-docs-sdk-python/](https://github.com/MicrosoftDocs/azure-docs-sdk-python/).

- Atualmente estamos atualizando as bibliotecas do Azure para Python para compartilhar padrões de nuvem comuns, como protocolos de autenticação, registro em log, rastreamento, protocolos de transporte, respostas em buffer e novas tentativas.

  - Essa funcionalidade compartilhada está presente na biblioteca [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core).

  - As bibliotecas que atualmente funcionam com a biblioteca principal estão listadas nas [Versões mais recentes do SDK do Azure para Python](azure-sdk-library-package-index.md#libraries-using-azurecore). Essas bibliotecas, principalmente as bibliotecas de cliente, são, às vezes, chamadas de "faixa 2".

  - As bibliotecas de gerenciamento e quaisquer outras que ainda não foram atualizadas são chamadas, às vezes, de "faixa 1".

- Para obter detalhes sobre as diretrizes que aplicamos às bibliotecas, consulte as [Diretrizes do Python: Introdução](https://azure.github.io/azure-sdk/python_introduction.html).

## <a name="provision-and-manage-azure-resources-with-management-libraries"></a>Provisionar e gerenciar recursos do Azure com bibliotecas de gerenciamento

As bibliotecas de *gerenciamento* do SDK (ou “plano de gerenciamento”) com nomes que começam com `azure-mgmt-` ajudam a criar, provisionar e de gerenciar recursos do Azure a partir de scripts do Python. Todos os serviços do Azure têm bibliotecas de gerenciamento correspondentes.

Com as bibliotecas de gerenciamento, é possível escrever scripts de configuração e de implantação para executar as mesmas tarefas que podem ser acessadas pelo [portal do Azure](https://portal.azure.com) ou pela [CLI do Azure](/cli/azure/install-azure-cli). (Como observamos anteriormente, a CLI do Azure é escrita em Python e usa as bibliotecas de gerenciamento para implementar seus vários comandos.)

Para obter detalhes sobre como trabalhar com cada biblioteca de gerenciamento, veja o arquivo *README.md* ou *README.rst* localizado na pasta de projeto da biblioteca no [repositório GitHub do SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk). Também é possível encontrar trechos de código adicionais na [documentação de referência](/python/api?view=azure-python) e nas [Amostras do Azure](https://docs.microsoft.com/samples/browse/?languages=python&products=azure).

## <a name="connect-to-and-use-azure-resources-with-client-libraries"></a>Conectar e usar os recursos do Azure com bibliotecas de clientes

As bibliotecas de *cliente* do SDK (ou “plano de dados”) ajudam a escrever código de aplicativos Python para interagir com serviços já provisionados. As bibliotecas de clientes existem somente para os serviços que dão suporte a um API de cliente.

Para obter detalhes sobre como trabalhar com cada biblioteca de clientes, veja o arquivo *README.md* ou *README.rst* localizado na pasta de projeto da biblioteca no [repositório GitHub do SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk). Também é possível encontrar trechos de código adicionais na [documentação de referência](/python/api?view=azure-python) e nas [Amostras do Azure](https://docs.microsoft.com/samples/browse/?languages=python&products=azure).

## <a name="get-help-and-connect-with-the-sdk-team"></a>Obtenha ajuda e conecte-se com a equipe do SDK

- Visite a [documentação do Python para bibliotecas do Azure](https://aka.ms/python-docs)
- Poste perguntas para a comunidade no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python)
- Problemas em aberto no SDK no [GitHub](https://github.com/Azure/azure-sdk-for-python/issues)
- Mencione [@AzureSDK](https://twitter.com/AzureSdk/) no Twitter

## <a name="next-step"></a>Próxima etapa

Nós recomendamos fortemente fazer uma configuração única de seu ambiente de desenvolvimento local para que você possa facilmente usar qualquer uma das bibliotecas do Azure para Python.

> [!div class="nextstepaction"]
> [Configurar seu ambiente de desenvolvimento local >>>](configure-local-development-environment.md)
