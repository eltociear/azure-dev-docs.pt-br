---
title: Criar um fluxo de trabalho do Aplicativo Lógico
description: Criar um fluxo de trabalho do Aplicativo Lógico
author: sptramer
manager: carmonm
ms.devlang: python
ms.topic: conceptual
ms.date: 6/15/2017
ms.author: sttramer
ms.openlocfilehash: 865da8128810904838eb4e517a2e38fe3eec3a82
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68285457"
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

