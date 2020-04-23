---
title: Diferenças entre os produtos da CLI do Azure
description: Entenda como os produtos da CLI do Azure são nomeados e têm a versão controlada, e como atualizá-los.
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 3cd61d2166d03f7b9d58db1ee1cee77d17b5b336
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030828"
---
# <a name="differences-between-azure-cli-products"></a>Diferenças entre os produtos da CLI do Azure

A partir do final de junho de 2018, os números de versão explícitos foram removidos dos nomes de produto da CLI do Azure. Essa alteração ajuda a eliminar a confusão que às vezes aparecia na documentação, em que os usuários eram instruídos a usar "a CLI do Azure", mas não ficava claro qual versão do produto estava sendo referenciada. Se você estiver familiarizado com os nomes dos produto antigos, veja como eles foram alterados:

* As versões da CLI do Azure 2.0 e posteriores agora são chamadas apenas de "CLI do Azure".
* As versões mais antigas da CLI do Azure (1.x e anteriores) agora são chamadas de "CLI clássica do Azure".

A alteração do nome para CLI clássica do Azure deixa claro que essa ferramenta destina-se a ser usada apenas com o modelo de implantação clássico. A CLI clássica é também não é mais atualizada ou recebe manutenção. Por essa e outras razões, é recomendável que você mude todas as implantações clássicas para usar o modelo do Azure Resource Manager e migre para a versão mais recente da CLI do Azure disponível.

Se você ainda está usando a CLI clássica, pode aprender sobre o processo de migração nos seguintes artigos:

* [Migrando do Clássico para o Azure Resource Manager](/azure/virtual-machines/linux/migration-classic-resource-manager-overview)
* [Instalar a CLI do Azure](install-azure-cli.md)
* [Migrando da CLI clássica do Azure para a CLI do Azure](https://github.com/Azure/azure-cli/blob/dev/doc/classic_cli_migration.md)
