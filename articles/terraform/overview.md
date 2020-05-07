---
title: Usando o Terraform com o Azure
description: Saiba como o Terraform pode ajudar a implantar e criar a versão de sua infraestrutura no Azure.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 63f17be153d8c1683eb6aff98552834cc88b8f03
ms.sourcegitcommit: 756e4873f904db954a56c20ebb2f1f5116ee4596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82171102"
---
# <a name="terraform-with-azure"></a>Terraform com o Azure

O [Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de software livre para provisionar e gerenciar infraestruturas de nuvem. Ele codifica a infraestrutura em arquivos de configuração que descrevem a topologia dos recursos de nuvem. Esses recursos incluem máquinas virtuais, contas de armazenamento e adaptadores de rede. A CLI do Terraform fornece um mecanismo simples para implantar e controlar a versão de arquivos de configuração para o Azure.

Este artigo descreve os benefícios de usar o Terraform para gerenciar a infraestrutura do Azure.

## <a name="automate-infrastructure-management"></a>Automatizar o gerenciamento de infraestrutura.

Os arquivos de configuração com base em modelo do Terraform permitem definir, provisionar e configurar recursos do Azure de forma repetível e previsível. Automatizar a infraestrutura traz várias vantagens:

- Reduz a possibilidade de erros humanos durante a implantação e gerenciamento da infraestrutura.
- Implanta o mesmo modelo várias vezes para criar ambientes de desenvolvimento, teste e produção idênticos.
- Reduz o custo de ambientes de desenvolvimento e teste ao criá-los sob demanda.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Entender as alterações de infraestrutura antes de serem aplicadas

À medida que um recurso de topologia se torna complexo, pode ser difícil entender o significado e o impacto das alterações de infraestrutura.

A CLI do Terraform permite que os usuários validem e visualizem as alterações de infraestrutura antes do aplicativo. Visualizar as alterações de infraestrutura de maneira segura traz diversos benefícios:
- Os membros da equipe podem colaborar com mais eficiência compreendendo rapidamente as alterações propostas e seu impacto.
- Alterações inadvertidas podem ser detectadas logo no início do processo de desenvolvimento

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Implantar a infraestrutura para várias nuvens

O Terraform é adepto à implantação de uma infraestrutura em vários provedores de nuvem. Ele permite que os desenvolvedores usem ferramentas consistentes para gerenciar cada definição de infraestrutura.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do Terraform e seus benefícios, aqui estão as próximas etapas sugeridas:

- Comece [instalando e configurando o Terraform para usá-lo no Azure](install-configure.md).
- [Criar uma máquina virtual do Azure usando o Terraform](create-linux-virtual-machine-with-infrastructure.md)
- Explorar o [módulo do Azure Resource Manager para Terraform](https://www.terraform.io/docs/providers/azurerm/) 