---
title: Tutorial – Teste de conformidade com o Terraform e o Azure
description: Saiba como aplicar testes de conformidade em estilo BDD (Desenvolvimento Orientado por Comportamento) às configurações do Terraform
ms.topic: tutorial
ms.date: 07/31/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 7abb4072d923d4d5ec4fa3df6251f07576dba3bc
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241308"
---
# <a name="tutorial-compliance-testing-with-terraform-and-azure"></a>Tutorial: Teste de conformidade com o Terraform e o Azure

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

Neste artigo, você aprende a realizar as seguintes tarefas:

> [!div class="checklist"]
> * Entender quando usar o teste de conformidade
> * Saber como fazer uma verificação de conformidade

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Instalar o Terraform**: Com base em seu ambiente, [baixar e instalar o Terraform](https://www.terraform.io/downloads.html).
- **Docker**: [Instalar o Docker](https://docs.docker.com/get-docker/)
- **Terraform-compliance**: [Instalar a ferramenta de conformidade do Terraform](https://terraform-compliance.com/pages/installation/docker).
- **Bifurcar os exemplos de teste**: Bifurcar o [projeto de exemplo do Terraform no GitHub](https://github.com/Azure/terraform) e cloná-lo em seu computador de desenvolvimento/teste.

## <a name="what-is-compliance-testing"></a>O que é teste de conformidade

O teste de conformidade é uma técnica de teste não funcional para determinar se um sistema atende aos padrões prescritos. O teste de conformidade também é conhecido como *teste de cumprimento*.

A maioria das equipes de software faz uma análise para verificar se os padrões estão sendo aplicados e implementados corretamente. Em geral trabalhando simultaneamente para melhorar os padrões que, por sua vez, levam à melhora da qualidade.

O teste de conformidade verifica se a saída de cada fase do ciclo de vida de desenvolvimento está de acordo com os requisitos acordados.

As verificações de conformidade devem ser integradas ao ciclo de desenvolvimento no início dos projetos. A tentativa de adicionar verificações de conformidade em uma fase posterior torna-se cada vez mais difícil quando o próprio requisito não está devidamente documentado.

## <a name="understanding-compliance-checks"></a>Entender as verificações de conformidade

A realização de verificações de conformidade é uma tarefa direta. Um conjunto de padrões e procedimentos é desenvolvido e documentado para cada fase do ciclo de vida de desenvolvimento. A saída de cada fase é comparada com os requisitos documentados. Os resultados do teste são eventuais "lacunas" quando não está em conformidade com os padrões predeterminados. O teste de conformidade é feito por meio do processo de inspeção e o resultado do processo de revisão deve ser documentado.

Vamos dar uma olhada em um exemplo específico.

Um problema comum são ambientes que apresentam falha quando vários desenvolvedores aplicam alterações incompatíveis. Digamos que uma pessoa esteja trabalhando em uma alteração e aplique recursos como, por exemplo, a criação de uma VM em um ambiente de teste. Então, outra pessoa aplica uma versão diferente do código que provisiona uma versão diferente daquela VM. Neste cenário, é preciso dispor de uma supervisão para verificar a conformidade com as regras estabelecidas.

Uma forma de resolver esse problema seria definir uma política de marcação de recursos, como ocorre com as marcas `role` e `creator`. Após definir as políticas, uma ferramenta como o [Terraform-compliance](https://terraform-compliance.com) será usada para garantir que as políticas sejam seguidas.

O Terraform-compliance se concentra em *testes negativos*. O teste negativo é o processo de verificar se um sistema é capaz de gerenciar com tranquilidade entradas inesperadas ou comportamentos indesejados. O *teste de fuzzing* é um exemplo de teste negativo. Com o teste de fuzzing, um sistema que recebe entradas é testado para garantir que seja capaz de gerenciar com segurança entradas inesperadas.

Felizmente, o Terraform é uma camada de abstração para qualquer API que cria, atualiza ou destrói entidades de infraestrutura de nuvem. O Terraform também verifica se a configuração local e as respostas da API remota estão em sincronia. Como o Terraform é usado principalmente em APIs de nuvem, ainda precisamos de uma forma de garantir que o código implantado na infraestrutura siga políticas específicas. O Terraform-compliance, uma ferramenta gratuita e de software livre, fornece essa funcionalidade para configurações do Terraform.

Usando o exemplo da VM, uma política de conformidade pode ser a seguinte: *"Se você estiver criando um recurso do Azure, ele deverá conter uma marca"* .

A ferramenta Terraform-compliance fornece uma estrutura de teste na qual você cria políticas como no exemplo. Em seguida, você executa essas políticas no plano de execução do Terraform.

O Terraform-compliance permite que você aplique os princípios do BDD (*Desenvolvimento Orientado por Comportamento*). O BDD é um processo de colaboração em que todos os stakeholders trabalham juntos para definir o que um determinado sistema deve fazer. Esses stakeholders geralmente incluem desenvolvedores, testadores e qualquer pessoa que tenha interesse no sistema que está sendo desenvolvido ou que seja afetada por ele. O objetivo do BDD é incentivar as equipes a criar exemplos concretos que expressem um entendimento comum sobre como o sistema deve se comportar.

## <a name="looking-at-an-example"></a>Examinando um exemplo

Anteriormente neste artigo, você leu sobre um exemplo de teste de conformidade que criava uma VM para um ambiente de teste. Esta seção mostra como traduzir aquele exemplo em um cenário e recurso do BDD. A regra é expressa primeiro usando o *Cucumber*, que é uma ferramenta usada para dar suporte ao BDD.

```Cucumber
when creating Azure resources, every new resource should have a tag
```

A regra anterior é traduzida da seguinte maneira:

```Cucumber
If the resource supports tags
Then it must contain a tag
And its value must not be null
```

Então, o código HCL do Terraform faria adesão à regra conforme a seguir.

```hcl
resource "random_uuid" "uuid" {}

resource "azurerm_resource_group" "rg" {
  name     = "rg-hello-tf-${random_uuid.uuid.result}"
  location = var.location

  tags = {
    environment = "dev"
    application = "Azure Compliance"
  } 
}
```

A primeira política poderia ser escrita como um [cenário de recurso do BDD](https://cucumber.io/docs/gherkin/reference/), da seguinte maneira:

```Cucumber
Feature: Test tagging compliance  # /target/src/features/tagging.feature
    Scenario: Ensure all resources have tags
        If the resource supports tags
        Then it must contain a tag
        And its value must not be null
```

O seguinte código mostra um teste para uma marca específica:

```Cucumber
Scenario Outline: Ensure that specific tags are defined
    If the resource supports tags
    Then it must contain a tag <tags>
    And its value must match the "<value>" regex

    Examples:
      | tags        | value              |
      | Creator     | .+                 |
      | Application | .+                 |
      | Role        | .+                 |
      | Environment | ^(prod\|uat\|dev)$ |
```

## <a name="running-the-sample"></a>Executando o exemplo

Nesta seção, você baixará e testará o exemplo.

1. [Baixe o exemplo de teste de conformidade](https://github.com/Azure/terraform/tree/master/samples/compliance-testing).

1. Altere os diretórios para o diretório `src`.

1. Execute o comando [terraform init](https://www.terraform.io/docs/commands/init.html) para inicializar o diretório de trabalho. Esta etapa faz o download dos módulos do Azure necessários para criar um grupo de recursos do Azure.

    ```bash
    terraform init
    ```
    
1. Execute o comando [terraform validate](https://www.terraform.io/docs/commands/validate.html) para validar a sintaxe dos arquivos de configuração.

    ```bash
    terraform validate
    ```
    
1. Execute o comando [terraform plan](https://www.terraform.io/docs/commands/plan.html) para criar um plano de execução.

    ```bash
    terraform plan -out tf.out
    ```
    
1. Execute a [aplicação do Terraform](https://www.terraform.io/docs/commands/apply.html) para aplicar o plano de execução.

    ```bash
    terraform apply -target=random_uuid.uuid
    ```
    
1. Execute o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar a imagem do Terraform-compliance.

    ```bash
    docker pull eerkunt/terraform-compliance
    ```
    
1. Execute o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar os testes em um contêiner do Docker. **O teste falhará**. A primeira regra que requer a existência de marcas é bem-sucedida. No entanto, a segunda regra falha porque as marcas `Role` e `Creator` estão ausentes.

    ```bash
    docker run --rm -v $PWD:/target -it eerkunt/terraform-compliance -f features -p tf.out
    ```
    
    ![Exemplo de teste com falha](media/best-practices-compliance-testing/best-practices-compliance-testing-tagging-fail.png)

1. Modifique `main.tf` conforme a seguir para corrigir o erro.

    ```terraform
      tags = {
        Environment = "dev"
        Application = "Azure Compliance"
        Creator     = "Azure Compliance"
        Role        = "Azure Compliance"
      } 
    
    ```
    
1. Execute `terraform validate` novamente para verificar a sintaxe.

    ```bash
    terraform validate
    ```
    
1. Execute `terraform plan` novamente para criar um plano de execução.

    ```bash
    terraform plan -out tf.out
    ```
    
1. Execute [docker run](https://docs.docker.com/engine/reference/commandline/run/) novamente para testar a configuração. Desta vez o teste é bem sucedido, pois a especificação completa foi implementada.

    ```bash
    docker run --rm -v $PWD:/target -it eerkunt/terraform-compliance -f features -p tf.out
    ```

    ![Exemplo de teste bem-sucedido](media/best-practices-compliance-testing/best-practices-compliance-testing-tagging-succeed.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar e executar testes de ponta a ponta em projetos do Terraform](best-practices-end-to-end-testing.md)
