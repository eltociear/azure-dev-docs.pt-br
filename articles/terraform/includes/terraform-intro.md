---
title: incluir arquivo
description: incluir arquivo
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 05/31/2020
ms.author: tarcher
ms.openlocfilehash: 43e684a44c657ab44f3f8f13719e08f0e339f498
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329894"
---
Introdução ao [Terraform](https://www.terraform.io) ao configurar [Terraform no Azure](https://www.terraform.io/docs/providers/azurerm/index.html) e criar um grupo de recursos básico do Azure.

O Terraform permite a definição, a visualização e a implantação da infraestrutura de nuvem. Usando o Terraform, você cria arquivos de configuração usando [sintaxe de HCL](https://www.terraform.io/docs/configuration/syntax.html). A sintaxe da HCL permite que você especifique o provedor de nuvem, como o Azure, e os elementos que compõem sua infraestrutura de nuvem. Depois de criar os arquivos de configuração, você cria um *plano de execução* que permite visualizar as alterações de infraestrutura antes de serem implantadas. Depois de verificar as alterações, aplique o plano de execução para implantar a infraestrutura.
