---
title: Tutorial – Configuração de teste de ponta a ponta com o Terratest em projetos do Terraform
description: Saiba mais sobre os testes de ponta a ponta com o Terratest em um projeto do Terraform.
ms.topic: tutorial
ms.date: 07/31/2020
ms.openlocfilehash: 7e287ea1f15d539182b0f9bef135ce7ebcc65d5f
ms.sourcegitcommit: b224b276a950b1d173812f16c0577f90ca2fbff4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810599"
---
# <a name="tutorial-setup-end-to-end-terratest-testing-on-terraform-projects"></a>Tutorial: Configuração de teste de ponta a ponta com o Terratest em projetos do Terraform

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

Neste artigo, você aprende a realizar as seguintes tarefas:

> [!div class="checklist"]
> * Entender os conceitos básicos de testes de ponta a ponta com o [Terratest](https://github.com/gruntwork-io/terratest)
> * Aprender a escrever um teste de ponta a ponta usando o Golang
> * Aprender a usar o Azure DevOps para disparar automaticamente testes de ponta a ponta quando o código está confirmado em seu repositório

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Instalar o Terraform**: Com base em seu ambiente, [baixar e instalar o Terraform](https://www.terraform.io/downloads.html).
- **Exemplos de teste de bifurcação:** para começar rapidamente, é recomendável bifurcar [este repositório](https://github.com/Azure/terraform) em sua própria organização do GitHub.
- **Linguagem de programação Go**: os casos de teste do Terraform foram escritos em [Go](https://golang.org/dl/). O exemplo deste artigo usa [módulos Go](https://blog.golang.org/using-go-modules). Recomenda-se Go 1.13 (ou posterior) para este artigo.

## <a name="what-is-end-to-end-testing"></a>O que é um teste de ponta a ponta

Testes de ponta a ponta validam se um sistema funciona como um todo. Esse tipo de teste é o oposto de testar módulos específicos. Para projetos do Terraform, o teste de ponta a ponta permite a validação do que foi implantado. Esse tipo de teste difere de muitos outros tipos que testam cenários pré-implantação. Os testes de ponta a ponta são essenciais para testar sistemas complexos que incluem vários módulos e operam vários recursos. Nesses cenários, o teste de ponta a ponta é a única maneira de determinar se os vários módulos estão interagindo corretamente.

Este artigo se concentra no uso do [Terratest](https://github.com/gruntwork-io/terratest) para implementar testes de ponta a ponta. O Terratest fornece todo o direcionamento necessário para realizar a seguinte tarefa:

- Implantar uma configuração do Terraform
- Permite que você escreva um teste usando a linguagem Go para validar o que foi implantado
- Orquestrar os testes em fases
- Desmontar a infraestrutura implantada

## <a name="tutorial-scenario"></a>Cenário do tutorial

Para este tutorial, estamos usando um exemplo disponível em [Azure/repositório de exemplo do terraform](https://github.com/Azure/terraform/blob/master/samples/end-to-end-testing/README.md).

Este exemplo define uma configuração do Terraform que implanta duas máquinas virtuais do Linux na mesma rede virtual. Uma VM, denominada `vm-linux-1`, tem endereço IP público definido. Somente a porta 22 está aberta para permitir conexões SSH. A segunda VM, `vm-linux-2`, não tem endereço IP público definido.

Nosso teste deve validar os seguintes cenários:

- A infraestrutura foi implantada corretamente
- Usando a porta 22, é possível abrir uma sessão SSH para `vm-linux-1`
- Usando a sessão SSH em `vm-linux-1`, é possível executar ping em `vm-linux-2`

![Cenário de teste de ponta a ponta de exemplo](media/best-practices-end-to-end-testing/scenario.png)

Se você [baixou o exemplo](#prerequisites), a configuração do Terraform para esse cenário pode ser encontrada no arquivo `src/main.tf`. Esse arquivo contém tudo o que é necessário para implantar a infraestrutura do Azure representada na figura anterior.

Se você não estiver familiarizado com a criação de máquinas virtuais, confira [Criar uma VM Linux com a infraestrutura do Azure usando o Terraform](create-linux-virtual-machine-with-infrastructure.md).

> [!CAUTION]
> O cenário de exemplo apresentado neste artigo é apenas para fins ilustrativos. Nós procuramos manter as coisas simples para se concentrar nas etapas do teste de ponta a ponta. Não é recomendável ter máquinas virtuais de produção que exponham portas SSH em um endereço IP público.

## <a name="end-to-end-test"></a>Teste de ponta a ponta

O teste de ponta a ponta é escrito em linguagem Go e usa a estrutura do Terratest. Se você [baixou o exemplo](#prerequisites), ele está definido no arquivo `src/test/end2end_test.go`.

O seguinte código-fonte mostra a estrutura padrão de um teste da Golang usando o Terratest:

```Go
package test

import (
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    test_structure "github.com/gruntwork-io/terratest/modules/test-structure"
)

func TestEndToEndDeploymentScenario(t *testing.T) {
    t.Parallel()

    fixtureFolder := "../"

    // User Terratest to deploy the infrastructure
    test_structure.RunTestStage(t, "setup", func() {
        terraformOptions := &terraform.Options{
            // Indicate the directory that contains the Terraform configuration to deploy
            TerraformDir: fixtureFolder,
        }

        // Save options for later test stages
        test_structure.SaveTerraformOptions(t, fixtureFolder, terraformOptions)

        // Triggers the terraform init and terraform apply command
        terraform.InitAndApply(t, terraformOptions)
    })

    test_structure.RunTestStage(t, "validate", func() {
        // run validation checks here
        terraformOptions := test_structure.LoadTerraformOptions(t, fixtureFolder)
            publicIpAddress := terraform.Output(t, terraformOptions, "public_ip_address")
    })

    // When the test is completed, teardown the infrastructure by calling terraform destroy
    test_structure.RunTestStage(t, "teardown", func() {
        terraformOptions := test_structure.LoadTerraformOptions(t, fixtureFolder)
        terraform.Destroy(t, terraformOptions)
    })
}
```

Como você pode ver no snippet de código anterior, o teste é composto por três fases:

- **setup**: Executa o Terraform para implantar a configuração
- **validate**: Faz as verificações e declaração de validação
- **teardown**: Limpa a infraestrutura após a execução do teste

A seguinte lista mostra algumas das principais funções fornecidas pela estrutura do Terratest:

- **terraform.InitAndApply**: Permite executar `terraform init` e `terraform apply` do código Go
- **terraform.Output**: Recupera o valor da variável de saída da implantação.
- **terraform.Destroy**: Executa o comando `terraform destroy` do código Go.
- **test_structure.LoadTerraformOptions**: Carrega opções do Terraform, como a configuração e as variáveis, do estado
- **test_structure.SaveTerraformOptions**: Salva opções do Terraform, como a configuração e as variáveis, no estado

## <a name="run-the-end-to-end-test"></a>Executar o teste de ponta a ponta

Nesta seção, você executa o teste na configuração e implantação de exemplo. 

1. Abra uma janela bash/terminal.

1. Faça logon na sua conta do Azure. Para obter informações sobre como fazer logon em sua assinatura do Azure, confira [Seção de autenticação do Azure](get-started-cloud-shell.md#authenticate-to-azure).

1. Para executar este teste de exemplo, você precisa de um nome de par de chaves privadas/públicas SSH `id_rsa` e `id_rsa.pub` em seu diretório base. Substitua `USER` pelo nome de seu diretório base.

```bash
export TEST_SSH_KEY_PATH="/home/USER/.ssh/id_rsa"
```

1. Altere os diretórios para o diretório `test`.

1. Execute o teste.

```go
go test -v ./ -timeout 10m
```

O teste exibirá resultados semelhantes à seguinte saída:

```output
--- PASS: TestEndToEndDeploymentScenario (390.99s)
PASS
ok      test    391.052s
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visão geral do teste do Terraform](best-practices-testing-overview.md)