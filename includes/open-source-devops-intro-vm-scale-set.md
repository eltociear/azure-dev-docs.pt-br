---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: 359048deda000224fb75b6ee777a55711f5de4a0
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80741284"
---
Os [conjuntos de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/articles/virtual-machine-scale-sets/overview) são um recurso do Azure que permite configurar um grupo de VMs idênticas e com balanceamento de carga. Não há nenhum custo adicional para os conjuntos de dimensionamento e eles são criados usando máquinas virtuais. Você paga apenas pelos recursos de computação subjacentes, como as instâncias de VM, os balanceadores de carga ou o armazenamento do Managed Disks. Com conjuntos de dimensionamento, as camadas de automação e gerenciamento são fornecidas para executar e dimensionar seus aplicativos. Em vez disso, você poderia criar e gerenciar VMs individuais manualmente. No entanto, há dois benefícios principais ao usar conjuntos de dimensionamento. Eles são criados no Azure e eles dimensionam automaticamente suas máquinas virtuais para atender às necessidades do aplicativo.