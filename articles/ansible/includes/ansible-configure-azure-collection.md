---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/09/2020
ms.author: tarcher
ms.openlocfilehash: 4f1fbe75f974aadc9e92e518e0e84d49ee73ed3d
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81755219"
---
- **Configurar a coleção do Azure**: execute o comando a seguir em uma janela do terminal para instalar a coleção do Azure. Se a coleção do Azure já estiver instalada, o sinalizador `--force` fará a atualização para a versão mais recente.

    ```bash
    ansible-galaxy collection install azure.azcollection --force
    ```
