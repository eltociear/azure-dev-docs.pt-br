---
title: Tutorial – Teste de integração com o Terraform e o Azure
description: Saiba mais sobre os testes de integração e como usar o Azure DevOps para configurar a integração contínua para projetos do Terraform.
ms.topic: tutorial
ms.date: 07/31/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: d6c8f9c419070d734c3c848163c52e6255d5512a
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831992"
---
# <a name="tutorial-configure-integration-tests-for-terraform-projects-in-azure"></a>Tutorial: Configurar testes de integração para projetos do Terraform no Azure

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

Neste artigo, você aprende a realizar as seguintes tarefas:

> [!div class="checklist"]
> * Conheça os conceitos básicos do teste de integração para projetos do Terraform.
> * Use o Azure DevOps para configurar um pipeline de integração contínua.
> * Execute uma análise de código estática no código do Terraform.
> * Execute `terraform validate` para validar os arquivos de configuração do Terraform no computador local.
> * Execute `terraform plan` para validar os arquivos de configuração do Terraform de uma perspectiva de serviços remotos.
> * Use um Pipeline do Azure para automatizar a integração contínua.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Organização e projeto do Azure DevOps**: Se você não tiver uma, [crie uma organização do Azure DevOps](/azure/devops/organizations/projects/create-project?tabs=preview-page&view=azure-devops).
- **Extensão das Tarefas de Compilação e Versão do Terraform**: [Instale a extensão das tarefas de compilação/versão do Terraform](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform) na organização do Azure DevOps.
- **Conceda ao Azure DevOps acesso à sua Assinatura do Azure**: Criar uma [conexão de serviço do Azure](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) chamada `terraform-basic-testing-azure-connection` para permitir que o Azure Pipelines se conecte às suas assinaturas do Azure
- **Instalar o Terraform**: Com base em seu ambiente, [baixar e instalar o Terraform](https://www.terraform.io/downloads.html).
- **Bifurcar os exemplos de teste**: Bifurcar o [projeto de exemplo do Terraform no GitHub](https://github.com/Azure/terraform) e cloná-lo em seu computador de desenvolvimento/teste.

## <a name="validate-a-local-terraform-configuration"></a>Validar uma configuração local do Terraform

O comando [terraform validate](https://www.terraform.io/docs/commands/validate.html) é executado na linha de comando, no diretório que contém os arquivos do Terraform. A meta principal desse comando é validar a sintaxe.

1. Abra o ambiente de linha de comando de sua preferência. Muitos editores de código, como o Visual Studio Code, fornecem uma interface de linha de comando.

1. Altere os diretórios para o diretório `samples/integration-testing/src` do repositório local. Ele contém um projeto simples do Terraform.

1. Inicialize a implantação do Terraform com [terraform init](https://www.terraform.io/docs/commands/init.html). Esta etapa faz o download dos módulos do Azure necessários para criar um grupo de recursos do Azure.

    ```bash
    terraform init
    ```

1. Valide o arquivo de teste do Terraform com o comando [terraform validate](https://www.terraform.io/docs/commands/validate.html).

    ```bash
    terraform validate
    ```

    Você deverá ver uma mensagem indicando que a configuração é válida.

1. Em um editor de código, abra o arquivo `main.tf`.

1. Na linha 5, insira um erro de grafia que invalide a sintaxe. Por exemplo, substitua `var.location` por `var.loaction`

1. Salve o arquivo.

1. Execute a validação novamente.

    ```bash
    terraform validate
    ```

    Desta vez, você verá uma mensagem de erro indicando a linha que contém o problema e uma descrição do erro.

Como você pode ver, o Terraform detectou um problema na sintaxe do código de configuração. Esse problema impede que a configuração seja implantada.

É uma prática recomendada sempre executar `terraform validate` em seus arquivos do Terraform antes de enviá-los ao sistema de controle de versão. Além disso, esse nível de validação deve fazer parte do pipeline de integração contínua. Posteriormente neste tutorial, exploraremos como [configurar um pipeline do Azure para validação automática](#automate-integration-tests-using-azure-pipeline).

## <a name="validate-terraform-configuration-can-be-deployed-on-azure"></a>A configuração do Terraform validada pode ser implantada no Azure

Na seção anterior, você viu como validar uma configuração do Terraform. Esse nível de teste foi específico para a sintaxe. O teste não levou em consideração o que poderia já estar implantado no Azure.

O Terraform é uma *linguagem declarativa*, o que significa que você declara o que deseja obter como resultado final. Por exemplo, digamos que você tenha 10 máquinas virtuais em um grupo de recursos. Então, você cria um arquivo do Terraform definindo três máquinas virtuais. A aplicação desse plano não incrementa a contagem total de VMs para 13. Em vez disso, o Terraform exclui sete das máquinas virtuais existentes para que você termine com apenas três. A execução de `terraform plan` permite que você confirme os possíveis resultados da aplicação de um plano de execução para evitar surpresas.

Para gerar o plano de execução do Terraform, execute o comando [terraform plan](https://www.terraform.io/docs/commands/plan.html). Esse comando se conecta à assinatura de destino do Azure para verificar que parte da configuração já está implantada. Então, o Terraform determina as alterações necessárias para atender aos requisitos declarados no arquivo do Terraform. Nesta fase, o Terraform não está implantando nada. Ele está informando o que acontecerá se você aplicar o plano.

Se você estiver acompanhando o tutorial e tiver implementado as etapas da seção anterior, execute o comando `terraform plan`:

```bash
terraform plan
```

Depois de executar `terraform plan`, o Terraform exibe o possível resultado da aplicação do plano de execução. A saída indica os recursos do Azure que serão adicionados, alterados e destruídos.

Por padrão, o Terraform armazena o estado no mesmo diretório local que o arquivo do Terraform. Esse padrão funciona bem em cenários de usuário único. No entanto, quando várias pessoas trabalham nos mesmos recursos do Azure, os arquivos de estado locais podem ficar fora de sincronia. Para corrigir esse problema, o Terraform dá suporte à gravação de arquivos de estado em um armazenamento de dados remoto (como o Armazenamento do Azure). Nesse cenário, pode ser problemático executar `terraform plan` em um computador local e ter como alvo um computador remoto. Como resultado, pode fazer sentido [automatizar essa etapa de validação como parte de seu pipeline de integração contínua](#automate-integration-tests-using-azure-pipeline).

## <a name="run-static-code-analysis"></a>Executar análise de código estática

A análise de código estática pode ser feita diretamente no código de configuração do Terraform, sem executá-lo. Essa análise pode ser útil para detectar problemas como falhas de segurança e inconsistências de conformidade.

As seguintes ferramentas fornecem análise estática para arquivos do Terraform:

- [Checkov](https://github.com/bridgecrewio/checkov/)
- [Terrascan](https://github.com/cesar-rodriguez/terrascan)
- [tfsec](https://github.com/liamg/tfsec) 
- [Deepsource](https://deepsource.io/blog/release-terraform-static-analysis/) 

A análise estática geralmente é executada como parte de um pipeline de integração contínua. Esses testes não exigem a criação de um plano de execução ou de uma implantação. Como resultado, eles são executados mais rapidamente do que outros testes e, geralmente, são executados primeiro no processo de integração contínua.

## <a name="automate-integration-tests-using-azure-pipeline"></a>Automatizar testes de integração usando um Pipeline do Azure

A integração contínua envolve testar o sistema inteiro quando uma alteração é introduzida. Nesta seção, você verá uma configuração de Pipeline do Azure usada para implementar a integração contínua.

1. Usando o editor de sua preferência, navegue até o clone local do [projeto de exemplo do Terraform no GitHub](https://github.com/Azure/terraform).

1. Abra o arquivo `samples/integration-testing/src/azure-pipeline.yaml`.

1. Role para baixo até a seção **etapas**, em que você verá um conjunto padrão de etapas usadas para executar várias rotinas de instalação e validação.

1. Examine a linha em que se lê, **Etapa 1: executar a Análise de Código Estática do Checkov**. Nesta etapa, o projeto `Checkov` mencionado anteriormente executa uma análise de código estática na configuração de exemplo do Terraform. 

    ```yaml
    - bash: $(terraformWorkingDirectory)/checkov.sh $(terraformWorkingDirectory)
      displayName: Checkov Static Code Analysis
    ```
    
    Observação:
    
    - Esse script é responsável por executar o Checkov no workspace do Terraform, montado dentro de um contêiner do Docker. Os agentes gerenciados pela Microsoft são habilitados para Docker. A execução de ferramentas dentro de um contêiner do Docker é mais fácil e elimina a necessidade de instalar o Checkov no agente de Pipeline do Azure.
    - A variável `$(terraformWorkingDirectory)` é definida no arquivo `azure-pipeline.yaml`.

1. Examine a linha em que se lê, **Etapa 2: instalar o Terraform no agente do Azure Pipelines**. A [extensão das Tarefas de Compilação e Versão do Terraform](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform) que você instalou anteriormente tem um comando para instalar o Terraform no agente que executa o Pipeline do Azure. Essa tarefa é o que está sendo feito nesta etapa.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-installer.TerraformInstaller@0
      displayName: 'Install Terraform'
      inputs:
        terraformVersion: $(terraformVersion)
    ```
    
    Observação:

    - A versão do Terraform a ser instalada é especificada por meio de uma variável do Pipeline do Azure chamada `terraformVersion` e definida no arquivo `azure-pipeline.yaml`.

1. Examine a linha em que se lê, **Etapa 3: executar o comando terraform init para inicializar o workspace**. Agora que o Terraform está instalado no agente, o diretório do Terraform pode ser inicializado.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform init'
      inputs:
        command: init
        workingDirectory: $(terraformWorkingDirectory)
    ```
    
    Observações:

    - A entrada `command` especifica qual comando do Terraform deve ser executado.
    - A entrada `workingDirectory` indica o caminho do diretório do Terraform.
    - A variável `$(terraformWorkingDirectory)` é definida no arquivo `azure-pipeline.yaml`.

1. Examine a linha em que se lê, **Etapa 4: executar o comando terraform validate para validar a sintaxe da HCL**. Depois que o diretório do projeto for inicializado, `terraform validate` será executado para validar a configuração no servidor.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform validate'
      inputs:
        command: validate
        workingDirectory: $(terraformWorkingDirectory)
    ```
    
1. Examine a linha em que se lê, **Etapa 5: executar o comando terraform plan para validar a sintaxe da HCL**. Conforme explicado anteriormente, a geração do plano de execução é feita para verificar se a configuração do Terraform é válida antes da implantação.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform plan'
      inputs:
        command: plan
        workingDirectory: $(terraformWorkingDirectory)
        environmentServiceName: $(serviceConnection)
        commandOptions: -var location=$(azureLocation)
    ```
    
    Observações:

    - A entrada `environmentServiceName` refere-se ao nome da conexão de serviço do Azure criada nos [pré-requisitos](#prerequisites). A conexão permite que o Terraform acesse sua assinatura do Azure.
    - A entrada `commandOptions` é usada para passar argumentos para o comando do Terraform. Nesse caso, um local está sendo especificado. A variável `$(azureLocation)` é definida anteriormente no arquivo YAML.

### <a name="import-the-pipeline-into-azure-devops"></a>Importar o pipeline para o Azure DevOps

1. Abra o projeto do Azure DevOps e vá para a seção do Azure Pipelines.
 
1. Selecione o botão **Criar Pipeline**.

1. Para a opção **Onde está seu código?** , selecione **GitHub (YAML)** .

    ![Onde está seu código?](media/best-practices-integration-testing/new-pipeline-where-github-yaml.png)

1. Neste ponto, talvez seja necessário autorizar o Azure DevOps a acessar sua organização. Para obter mais informações sobre este tópico, confira o artigo [Criar repositórios do GitHub](/azure/devops/pipelines/repos/github?view=azure-devops&tabs=yaml).

1. Na lista de repositórios, selecione a bifurcação do repositório que você criou na sua organização do GitHub.

1. Na etapa **Configurar seu pipeline**, escolha Iniciar de um pipeline YAML existente.

    ![Pipeline YAML existente](media/best-practices-integration-testing/new-pipeline-existing-yaml.png)

1. Quando a página **Selecionar pipeline YAML existente** for exibida, especifique o branch `master` e insira o caminho para o pipeline YAML: `samples/integration-testing/src/azure-pipeline.yaml`.

    ![Selecionar pipeline YAML existente](media/best-practices-integration-testing/select-existing-yaml-pipeline.png)

1. Selecione **Continuar** para carregar o pipeline YAML do Azure do GitHub.

1. Quando a página **Examinar o pipeline YAML** for exibida, selecione **Executar** para criar e disparar manualmente o pipeline pela primeira vez.

    ![Executar Pipeline do Azure](media/best-practices-integration-testing/run-pipeline.png)

### <a name="run-the-pipeline"></a>Executar o pipeline

Você pode executar o pipeline manualmente da interface do usuário do Azure DevOps. Contudo, o objetivo do artigo é mostrar a integração contínua automatizada. Teste o processo confirmando uma alteração na pasta `samples/integration-testing/src` do repositório bifurcado. A alteração vai disparar automaticamente um novo pipeline no branch em que você está efetuando push do código.

![Pipeline em execução do GitHub](media/best-practices-integration-testing/pipeline-running-from-github.png)

Depois de executar essa etapa, acesse os detalhes no Azure DevOps para verificar se tudo foi executado corretamente.

![Pipeline Green do Azure DevOps](media/best-practices-integration-testing/azure-devops-green-pipeline.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar e executar testes de conformidade em projetos do Terraform](best-practices-compliance-testing.md)