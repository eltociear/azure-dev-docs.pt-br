---
title: Criar um fluxo de trabalho do Aplicativo Lógico
description: Criar um fluxo de trabalho do Aplicativo Lógico
ms.topic: conceptual
ms.date: 6/15/2017
ms.custom: devx-track-python
ms.openlocfilehash: c14a575cb1a1dbc8caa88c8fe439fedbe0886c57
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87982638"
---
# <a name="create-a-logic-app-workflow"></a>Criar um fluxo de trabalho do Aplicativo Lógico

O código a seguir cria um fluxo de trabalho do aplicativo lógico.

```python
from azure.mgmt.logic.models import Workflow

group_name = 'myresourcegroup'
workflow_name = '12HourHeartBeat'
logic_client.workflows.create_or_update(
    group_name,
    workflow_name,
    Workflow(
        location = 'East US',
        definition={
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {},
            "triggers": {},
            "actions": {},
            "outputs": {}
        }
    )
)
```

