---
title: Como instalar pacotes de biblioteca do SDK do Azure para Python
description: Como instalar, desinstalar e verificar bibliotecas do SDK do Azure ou do Python usando pip. Inclui detalhes sobre como instalar versões específicas e pacotes de versão prévia.
ms.date: 05/26/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 310a4327029f808a5e09136aa77956ed8810f5b2
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983368"
---
# <a name="how-to-install-azure-library-packages-for-python"></a>Como instalar pacotes de biblioteca do Azure para Python

O SDK do Azure para Python é composto unicamente por muitas bibliotecas individuais listadas no [índice do pacote](azure-sdk-library-package-index.md). Você instala os pacotes de biblioteca específicos necessários para um projeto usando `pip install`.

Com essas bibliotecas, você pode provisionar e gerenciar recursos nos serviços do Azure (usando as bibliotecas de gerenciamento, que têm `-mgmt` em seus nomes) e conectar esses recursos do código do aplicativo (usando as bibliotecas de cliente).

## <a name="install-the-latest-version-of-a-library"></a>Instalar a versão mais recente de uma biblioteca

```cmd
pip install azure-storage-blob
```

```cmd
pip install azure-mgmt-storage
```

`pip install` recupera a versão mais recente de uma biblioteca no seu ambiente atual do Python.

Em sistemas Linux, você deve instalar uma biblioteca para cada usuário separadamente. Não há suporte para instalação de bibliotecas para todos os usuários com `sudo pip install`.

## <a name="install-specific-library-versions"></a>Instalar versões de bibliotecas específicas

```cmd
pip install azure-storage-blob==12.0.0
```

```cmd
pip install azure-mgmt-storage==10.0.0
```

Especifique a versão desejada na linha de comando com `pip install`.

## <a name="install-preview-packages"></a>Instalar pacotes de visualização

```cmd
pip install --pre azure-storage-blob
```

```cmd
pip install --pre azure-mgmt-storage
```

Para instalar a versão prévia mais recente de uma biblioteca, inclua o sinalizador `--pre` na linha de comando.

A Microsoft lança regularmente pacotes de bibliotecas de versão prévia que dão suporte a futuros recursos, com a ressalva de que a biblioteca está sujeita a alterações e não deve ser usada em projetos de produção.

## <a name="verify-a-library-installation"></a>Verificar uma instalação de biblioteca

```cmd
pip show azure-storage-blob
```

```cmd
pip show azure-mgmt-storage
```

Use `pip show <library>` para verificar se uma biblioteca está instalada. Se a biblioteca estiver instalada, o comando exibirá a versão e outras informações resumidas. Caso contrário, o comando não exibirá nada.

Também é possível usar `pip freeze` ou `pip list` para ver todas as bibliotecas instaladas em seu ambiente atual do Python.

## <a name="uninstall-a-library"></a>Desinstalar uma biblioteca

```cmd
pip uninstall azure-storage-blob
```

Para desinstalar uma biblioteca, use `pip uninstall <library>`.

