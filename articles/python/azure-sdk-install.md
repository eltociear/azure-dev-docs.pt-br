---
title: Instalar o SDK do Azure para bibliotecas do Python
description: Como instalar, desinstalar e verificar bibliotecas do SDK do Azure ou do Python usando pip. Inclui detalhes sobre como instalar versões específicas e pacotes de versão prévia.
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 172091bcdff5ba55ccc7c2f13c60f3c7d645efc1
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82138742"
---
# <a name="install-azure-sdk-for-python-libraries"></a>Instalar o SDK do Azure para bibliotecas do Python

O SDK do Azure para Python fornece uma API por meio da qual você pode interagir com o Azure do código Python. Os nomes de todas as bibliotecas atuais estão na [página de índice do SDK do Azure para o Python](https://azure.github.io/azure-sdk/releases/latest/all/python.html).

As bibliotecas cujos nomes começam com `azure-mgmt` são bibliotecas de *gerenciamento*, as quais você usa para provisionar e gerenciar recursos do Azure como no [portal do Azure](https://portal.azure.com) ou usando a [CLI do Azure](/cli/azure/install-azure-cli). Por exemplo, para provisionar e gerenciar recursos do Armazenamento do Azure, você usa a biblioteca `azure-mgmt-storage`.

Todas as outras bibliotecas no SDK são bibliotecas de *cliente*, as quais você usa por meio do código de aplicativo para trabalhar com recursos já provisionados. Por exemplo, para trabalhar com blobs do Armazenamento do Azure por meio do código de aplicativo, você usa a biblioteca `azure-storage-blob`.

## <a name="install-the-latest-version-of-a-library"></a>Instalar a versão mais recente de uma biblioteca

A execução de `pip install` instala a versão mais recente de uma biblioteca no ambiente atual do Python:

```bash
pip install azure-storage-blob
```

Em sistemas Linux, o SDK não dá suporte ao uso de `sudo pip install` para instalar uma biblioteca para todos os usuários. Cada usuário deve usar `pip install` separadamente.

## <a name="install-specific-library-versions"></a>Instalar versões de bibliotecas específicas

Caso precise instalar uma versão específica de uma biblioteca, especifique a versão na linha de comando:

```bash
pip install azure-storage-blob==12.0.0
```

## <a name="install-preview-packages"></a>Instalar pacotes de visualização

A Microsoft lança regularmente bibliotecas do SDK de versão prévia que dão suporte a futuros recursos, com a ressalva de que a biblioteca está sujeita a alterações e não deve ser usada em projetos de produção.

Para instalar a versão prévia mais recente de uma biblioteca, inclua o sinalizador `--pre` na linha de comando.

```bash
pip install --pre azure-storage-blob
```

## <a name="verify-a-library-installation"></a>Verificar uma instalação de biblioteca

Use `pip show <library>` para verificar se uma biblioteca está instalada. Se a biblioteca estiver instalada, o comando exibirá a versão e outras informações resumidas. Caso contrário, o comando não exibirá nada.

```bash
pip show azure-storage-blob
```

Também é possível usar `pip freeze` ou `pip list` para ver todas as bibliotecas instaladas em seu ambiente atual do Python.

## <a name="uninstall-a-library"></a>Desinstalar uma biblioteca

Para desinstalar uma biblioteca, use `pip uninstall <library>`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como usar o SDK](azure-sdk-get-started.yml)
