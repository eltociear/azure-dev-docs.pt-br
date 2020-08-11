---
title: Tutorial – Visão geral do teste do Terraform
description: Saiba mais sobre as diferentes opções de teste que você pode configurar para validar projetos do Terraform.
ms.topic: overview
ms.date: 07/31/2020
ms.openlocfilehash: e32d8f6c71045392b016699fa63d790995918201
ms.sourcegitcommit: e451e4360d9c5956cc6a50880b3a7a55aa4efd2f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507066"
---
# <a name="tutorial-terraform-testing-overview"></a>Tutorial: Visão geral do teste do Terraform

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

O Terraform é uma ferramenta de IaC (Infraestrutura como Código). Essa categoria de ferramenta refere-se ao fato de que você trata os arquivos do Terraform como faria com o código-fonte do projeto. Parte desse processo inclui controle de versão e controle do código-fonte. Além disso, os testes também devem fazer parte do processo. Este artigo fornece uma visão geral dos diferentes tipos de testes que podem ser executados em um projeto do Terraform.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="integration-testing"></a>Testes de integração

Os testes de integração validam se uma alteração de código introduzida recentemente não causa falha no código existente. No DevOps, a CI (integração contínua) se refere a um processo que compila o sistema inteiro sempre que a base do código é alterada, como alguém que deseja mesclar uma PR em um repositório do Git. A seguinte lista contém exemplos comuns de testes de integração:

- Ferramentas de análise de código estática, como lint e format.
- Executar o comando [terraform validate](https://www.terraform.io/docs/commands/validate.html) para verificar a sintaxe do arquivo de configuração.
- Executar o comando [terraform plan](https://www.terraform.io/docs/commands/validate.html) para verificar se a configuração funciona conforme o esperado.

> [!div class="nextstepaction"]
> [Saiba mais sobre o teste de integração](best-practices-integration-testing.md)

## <a name="unit-testing"></a>Teste de unidade

Os testes de unidade verificam se uma parte ou função específica de um programa se comporta corretamente. Os testes de unidade são escritos pelo desenvolvedor da funcionalidade. Às vezes chamado de Desenvolvimento Orientado por Testes, ou TDD, esse tipo de teste envolve ciclos de desenvolvimento curtos e contínuos. No contexto de projetos do Terraform, o teste de unidade pode passar a usar `terraform plan` a fim de verificar se os valores reais disponíveis no plano gerado são iguais aos valores esperados. 

O teste de unidade pode ser especialmente benéfico quando seus módulos do Terraform começam a se tornar mais complexos:

- Gerar blocos dinâmicos
- Usar loops
- Calcular variáveis locais

Assim como ocorre com os testes de integração, muitas vezes os testes de unidade são incluídos no processo de integração contínua.

## <a name="compliance-testing"></a>Teste de conformidade

O teste de conformidade é usado para verificar se a configuração segue as políticas que você definiu para o projeto. Por exemplo, você pode definir convenções de nomenclatura geopolítica para os recursos do Azure. Ou pode querer que as máquinas virtuais sejam criadas com base em um subconjunto definido de imagens. O teste de conformidade é usado para impor regras como essas.

Normalmente, o teste de conformidade também é definido como parte do processo de integração contínua.

> [!div class="nextstepaction"]
> [Saiba mais sobre o teste de conformidade](best-practices-compliance-testing.md)

## <a name="end-to-end-e2e-testing"></a>Teste E2E (de ponta a ponta)

Os testes E2E validam o funcionamento de um programa antes de implantá-lo em produção. Um cenário de exemplo seria um módulo do Terraform implantando duas máquinas virtuais em uma rede virtual. Talvez você queira impedir que as duas máquinas executem ping entre si. Neste exemplo, você pode definir um teste para verificar se o resultado pretendido foi alcançado antes da implantação.

O teste E2E normalmente consiste em um processo de três etapas. Primeiro, a configuração é aplicada a um ambiente de teste. Em seguida, o código é executado para verificar os resultados. Por fim, o ambiente de teste é reinicializado ou desativado (como ao desalocar uma máquina virtual).

> [!div class="nextstepaction"]
> [Saiba mais sobre o teste de ponta a ponta](best-practices-end-to-end-testing.md)