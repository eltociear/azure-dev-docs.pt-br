---
title: Instalar o SDK do Azure para bibliotecas do Python
description: Como instalar, desinstalar e verificar bibliotecas do SDK do Azure ou do Python usando pip. Inclui detalhes sobre como instalar versões específicas e pacotes de versão prévia.
ms.date: 05/13/2020
ms.topic: conceptual
ms.openlocfilehash: 302d17211480f9c8793d7be1de20a6ab5dec3c95
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83403669"
---
# <a name="install-azure-sdk-for-python-libraries"></a>Instalar o SDK do Azure para bibliotecas do Python

O SDK do Azure para Python fornece uma API por meio da qual você pode interagir com o Azure do código Python. Os nomes de todas as bibliotecas atuais estão na [página de índice do SDK do Azure para o Python](https://azure.github.io/azure-sdk/releases/latest/all/python.html).

As bibliotecas cujos nomes começam com `azure-mgmt` são bibliotecas de *gerenciamento*, as quais você usa para provisionar e gerenciar recursos do Azure como no [portal do Azure](https://portal.azure.com) ou usando a [CLI do Azure](/cli/azure/install-azure-cli). Por exemplo, para provisionar e gerenciar recursos do Armazenamento do Azure, você usa a biblioteca `azure-mgmt-storage`.

Todas as outras bibliotecas no SDK são bibliotecas de *cliente*, as quais você usa por meio do código de aplicativo para trabalhar com recursos já provisionados. Por exemplo, para trabalhar com blobs do Armazenamento do Azure por meio do código de aplicativo, você usa a biblioteca `azure-storage-blob`.

## <a name="install-the-latest-version-of-a-library"></a>Instalar a versão mais recente de uma biblioteca

```bash
pip install azure-storage-blob
```

`pip install` instala a versão mais recente de uma biblioteca no ambiente atual do Python.

Em sistemas Linux, o SDK não dá suporte ao uso de `sudo pip install` para instalar uma biblioteca para todos os usuários. Cada usuário deve usar `pip install` separadamente.

## <a name="install-specific-library-versions"></a>Instalar versões de bibliotecas específicas

```bash
pip install azure-storage-blob==12.0.0
```

Indique a especificação da versão na linha de comando com `pip install`.

## <a name="install-preview-packages"></a>Instalar pacotes de visualização

```bash
pip install --pre azure-storage-blob
```

Para instalar a versão prévia mais recente de uma biblioteca, inclua o sinalizador `--pre` na linha de comando.

A Microsoft lança regularmente bibliotecas do SDK de versão prévia que dão suporte a futuros recursos, com a ressalva de que a biblioteca está sujeita a alterações e não deve ser usada em projetos de produção.

## <a name="verify-a-library-installation"></a>Verificar uma instalação de biblioteca

```bash
pip show azure-storage-blob
```

Use `pip show <library>` para verificar se uma biblioteca está instalada. Se a biblioteca estiver instalada, o comando exibirá a versão e outras informações resumidas. Caso contrário, o comando não exibirá nada.

Também é possível usar `pip freeze` ou `pip list` para ver todas as bibliotecas instaladas em seu ambiente atual do Python.

## <a name="uninstall-a-library"></a>Desinstalar uma biblioteca

```bash
pip uninstall azure-storage-blob
```

Para desinstalar uma biblioteca, use `pip uninstall <library>`.

## <a name="next-steps"></a>Próximas etapas

Agora você está completamente pronto para escrever e executar códigos, o que pode ser feito usando qualquer um dos seguintes exemplos:

> [!div class="nextstepaction"]
> [Exemplo: Criar um grupo de recursos >>>](azure-sdk-example-resource-group.md)
