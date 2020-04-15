---
title: Instalar o SDK do Azure para Python
description: Como instalar o SDK do Azure para Python usando pip ou GitHub. O SDK do Azure pode ser instalado como bibliotecas individuais ou como um pacote completo.
ms.date: 10/31/2019
ms.topic: conceptual
ms.custom: seo-python-october2019
ms.openlocfilehash: bf74567e5018c7d48141a6992cafc91e90045a25
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80441761"
---
# <a name="install-the-azure-sdk-for-python"></a>Instalar o SDK do Azure para Python

O SDK do Azure para Python fornece uma API por meio da qual você pode interagir com o Azure do código Python. Instale bibliotecas individuais do SDK de acordo com as suas necessidades.

O SDK do Azure para Python é testado e tem suporte para as versões 2.7 e 3.5.3+ do CPython e 5.4+ do PyPy. Os desenvolvedores também usam o SDK com outros interpretadores, como IronPython e Jython, mas é possível encontrar problemas isolados e incompatibilidades. Caso precise de um intérprete do Python, instale a versão mais recente de [python.org/downloads](https://www.python.org/downloads).

## <a name="install-sdk-libraries-using-pip"></a>Instalar bibliotecas do SDK usando pip

O SDK do Azure para Python é composto por várias bibliotecas individuais e cada uma delas provisiona ou funciona com serviços específicos do Azure. É possível instalar cada uma usando `pip install <library>`. Consulte a [página de versão do SDK](https://azure.github.io/azure-sdk/releases/latest/python.html) para obter instruções e documentação específicas para cada biblioteca.

Por exemplo, caso esteja usando o Armazenamento do Azure, você poderá instalar a biblioteca `azure-storage-file`, `azure-storage-blob` ou `azure-storage-queue`. Caso esteja usando as tabelas do Azure Cosmos DB, instale `azure-cosmosdb-table`. O Azure Functions tem suporte por meio da biblioteca `azure-functions` e assim por diante. As bibliotecas que começam com `azure-mgmt-` fornecem a API para provisionar recursos do Azure.

### <a name="install-specific-library-versions"></a>Instalar versões de bibliotecas específicas

Caso precise instalar uma versão específica de uma biblioteca, especifique a versão na linha de comando:

```bash
pip install azure-storage-blob==12.0.0
```

> [!NOTE]
> Em sistemas Linux, o SDK não dá suporte ao uso de `sudo pip install` para instalar uma biblioteca para todos os usuários. Cada usuário deve usar `pip install` separadamente. 

### <a name="install-preview-packages"></a>Instalar pacotes de visualização

A Microsoft libera regularmente bibliotecas do SDK na versão prévia que dão suporte aos recursos futuros. Para instalar a versão prévia mais recente de uma biblioteca, inclua o sinalizador `--pre` na linha de comando. 

```bash
# Install all preview versions of the Azure SDK for Python
pip install --pre azure

# Install the preview version for azure-storage-blob only.
pip install --pre azure-storage-blob
```

## <a name="verify-sdk-installation-details-with-pip"></a>Verifique os detalhes de instalação do SDK com pip

Use o comando `pip show <library>` para verificar se uma biblioteca foi instalada. Se a biblioteca estiver instalada, o comando exibirá a versão e outras informações de resumo. Se a biblioteca não estiver instalada, o comando não exibirá nada.

```bash
# Check installation of the Azure SDK for Python
pip show azure

# Check installation of a specific library
pip show azure-storage-blob
```

Também é possível usar `pip freeze` ou `pip list` para ver todas as bibliotecas instaladas em seu ambiente atual do Python.

## <a name="uninstall-azure-sdk-for-python-libraries"></a>Desinstalar o SDK do Azure para bibliotecas do Python

Para desinstalar uma biblioteca individual, use `pip uninstall <library>`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como usar o SDK](azure-sdk-get-started.yml)