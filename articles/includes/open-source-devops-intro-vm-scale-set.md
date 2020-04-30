---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: 2bf0a888cd2d4e49899b384f643430f5e30c95e8
ms.sourcegitcommit: 3c69d7c3e5c5a00a01ee18e63b0659830c7d4ec0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82109585"
---
Os [conjuntos de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) são um recurso do Azure que permite configurar um grupo de VMs idênticas e com balanceamento de carga. Não há nenhum custo adicional para os conjuntos de dimensionamento e eles são criados usando máquinas virtuais. Você paga apenas pelos recursos de computação subjacentes, como as instâncias de VM, os balanceadores de carga ou o armazenamento do Managed Disks. Com conjuntos de dimensionamento, as camadas de automação e gerenciamento são fornecidas para executar e dimensionar seus aplicativos. Em vez disso, você poderia criar e gerenciar VMs individuais manualmente. No entanto, há dois benefícios principais ao usar conjuntos de dimensionamento. Eles são criados no Azure e eles dimensionam automaticamente suas máquinas virtuais para atender às necessidades do aplicativo.