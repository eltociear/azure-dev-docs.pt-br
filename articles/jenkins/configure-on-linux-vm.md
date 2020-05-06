---
title: Início Rápido – Criar um servidor Jenkins no Azure
description: Saiba como instalar o Jenkins em uma máquina virtual do Linux do Azure por meio do modelo de solução do Jenkins e criar um aplicativo Java de exemplo.
keywords: jenkins, azure, devops, portal, linux, virtual machine, solution template
ms.topic: quickstart
ms.date: 03/03/2020
ms.openlocfilehash: 2ea038dad2294784804f45026ea26198a9b12d79
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82170332"
---
# <a name="quickstart-create-a-jenkins-server-on-an-azure-linux-vm"></a>Início Rápido: Criar um servidor Jenkins em uma VM do Linux do Azure

Este guia de início rápido mostra como instalar o [Jenkins](https://jenkins.io) em uma VM Linux Ubuntu com as ferramentas e os plug-ins configurados para funcionar com o Azure. Ao concluir, você terá um servidor Jenkins em execução no Azure criando um aplicativo Java de exemplo do [GitHub](https://github.com).

## <a name="prerequisites"></a>Pré-requisitos

* Acesso a SSH na linha de comando do computador (como o shell Bash ou o [PuTTY](https://www.putty.org/))

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Criar a VM Jenkins do modelo de solução

Jenkins dá suporte a um modelo onde o servidor Jenkins delega trabalho para um ou mais agentes para permitir que uma única instalação Jenkins hospede um número grande de projetos ou para fornecer ambientes diferentes, necessários para compilações ou testes. As etapas nesta seção o orientam durante a instalação e configuração de um servidor Jenkins no Azure.

1. No navegador, abra a [imagem do Azure Marketplace para Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Selecione **OBTENHA AGORA**.

    ![Selecione OBTENHA AGORA para iniciar o processo de instalação para a imagem do Jenkins Marketplace.](./media/install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Depois de consultar as informações de termos e detalhes de preços, selecione **Continuar**.

    ![Informações de termos e preços da imagem do Jenkins Marketplace.](./media/install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Selecione **Criar** para configurar o servidor de Jenkins no portal do Azure. 

    ![Instale a imagem do Jenkins Marketplace.](./media/install-from-azure-marketplace-image/jenkins-install-create.png)

1. Na guia **Noções Básicas**, especifique os seguintes valores:

   - **Nome** - Digite `Jenkins`.
   - **Nome de usuário** - Insira o nome de usuário a ser usado ao entrar na máquina virtual que está executando Jenkins. O nome de usuário deve atender aos [requisitos específicos](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
   - **Tipo de autenticação** - Selecione **Chave pública SSH**.
   - **Chave pública SSH** - Copie e cole uma chave pública RSA no formato de linha única (iniciando com `ssh-rsa`) ou no formato PEM de multilinhas. É possível gerar chaves SSH usando ssh-keygen no Linux e macOS ou PuTTYGen no Windows. Para obter mais informações sobre chaves SSH e Azure, consulte o artigo [Como usar chaves SSH com o Windows no Azure](/azure/virtual-machines/linux/ssh-from-windows).
   - **Assinatura** - Selecione a assinatura do Azure que você deseja para instalar o Jenkins.
   - **Grupo de recursos** - Selecione **Criar novo** e digite um nome para o grupo de recursos que serve como um contêiner lógico para a coleção de recursos que compõem sua instalação Jenkins.
   - **Local** - Selecione **Leste dos EUA**.

     ![Insira informações de grupo de recursos e autenticação para Jenkins na guia Básico.](./media/install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Selecione **OK** para prosseguir para a guia **Configurações Adicionais**. 

1. Na guia **Configurações Adicionais**, especifique os seguintes valores:

   - **Tamanho** - Selecione a opção de tamanho apropriado para sua máquina virtual de Jenkins.
   - **Tipo de disco de máquina virtual** - Especifique HDD (unidade de disco rígido) ou SSD (unidade de estado sólido) para indicar qual tipo de disco de armazenamento é permitido para as máquinas virtuais Jenkins.
   - **Rede virtual** - (opcional) Selecione **Rede virtual** para modificar as configurações padrão.
   - **Sub-redes** - Selecione **Sub-redes**, verifique as informações e selecione **OK**.
   - **Endereço IP público** - O nome do endereço IP padrão é o nome de Jenkins que você especificou na página anterior com um sufixo de - IP. Você pode selecionar a opção para alterar esse padrão.
   - **Rótulo de nome de domínio** - Especifique o valor para a URL totalmente qualificada para a máquina virtual Jenkins.
   - **Tipo de versão Jenkins** - Selecione o tipo de versão desejada entre as opções: `LTS`, `Weekly build` ou `Azure Verified`. As opções `LTS` e `Weekly build` são explicadas no artigo [Linha de versão Jenkins LTS](https://jenkins.io/download/lts/). A opção `Azure Verified` refere-se a uma [ versão Jenkins LTS](https://jenkins.io/download/lts/) que foi verificada para executar no Azure. 
   - **Tipo de JDK** – JDK a ser instalado. O padrão são builds certificados do OpenJDK testados por Zulu.

     ![Insira as configurações de máquina virtual para Jenkins na guia Configurações.](./media/install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Selecione **OK** para prosseguir para a guia **Configurações de Integração**.

1. Na guia **Configurações de Integração**, especifique os valores a seguir:

    - **Entidade de Serviço** - A entidade de serviço é adicionada ao Jenkins como uma credencial para autenticação com o Azure. `Auto` significa que a entidade será criada pela MSI (Identidade de Serviço Gerenciada). `Manual` significa que a entidade deve ser criada por você. 
        - **ID do Aplicativo** e **Segredo** - Se você selecionar a opção `Manual` para a opção **Entidade de Serviço**, será necessário especificar `Application ID` e `Secret` para a entidade de serviço. Ao [criar uma entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli), observe que a função padrão é **Colaborador**, que é suficiente para trabalhar com recursos do Azure.
    - **Habilitar agentes de nuvem** - Especifica o modelo de nuvem padrão para os agentes em que `ACI` faz referência à Instância do Contêiner do Azure e `VM` refere-se às máquinas virtuais. Também é possível especificar `No` se não desejar habilitar um agente de nuvem.

1. Selecione **OK** para prosseguir para a guia **Resumo**.

1. Quando a guia **Resumo** aparece, as informações inseridas são validadas. Depois de ver a mensagem **Validação aprovada** (na parte superior da guia), selecione **OK**. 

     ![Na guia Resumo exibe e valida as opções selecionadas.](./media/install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Quando a guia **Criar** aparecer, selecione **Criar** para criar a máquina virtual Jenkins. Quando o servidor estiver pronto, uma notificação aparecerá no portal do Azure.

     ![Notificação de que Jenkins está pronto.](./media/install-from-azure-marketplace-image/jenkins-install-notification.png)

## <a name="connect-to-jenkins"></a>Conectar-se ao Jenkins

1. Navegue até sua máquina virtual (por exemplo, `http://jenkins2517454.eastus.cloudapp.azure.com/`) no navegador da Web. O console do Jenkins não pode ser acessado por meio de HTTP não segura e, portanto, instruções são fornecidas na página para acessar o console do Jenkins com segurança do seu computador usando um túnel SSH.

    ![Desbloquear Jenkins](./media/install-solution-template-steps/jenkins-ssh-instructions.png)

1. Configure o túnel usando o comando `ssh` na página a partir da linha de comando, substituindo `username` pelo nome do usuário administrador da máquina virtual escolhido anteriormente durante a configuração da máquina virtual no modelo de solução.

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
    ```
    
1. Depois de iniciar o túnel, navegue para `http://localhost:8080/` no computador local. 

1. Obtenha a senha inicial executando o comando abaixo na linha de comando, enquanto estiver conectado à VM Jenkins por meio do SSH.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```
    
1. Desbloqueie o painel Jenkins pela primeira vez usando essa senha inicial.

    ![Desbloquear Jenkins](./media/install-solution-template-steps/jenkins-unlock.png)

1. Selecione **Instalar plug-ins sugeridos** na próxima página e crie um usuário administrador do Jenkins usado para acessar o painel do Jenkins.

    ![O Jenkins está pronto!](./media/install-solution-template-steps/jenkins-welcome.png)

O servidor Jenkins agora está pronto para compilar código.

## <a name="create-your-first-job"></a>Criar seu primeiro trabalho

1. Selecione **Criar novos trabalhos** no console do Jenkins, nomeie-o **mySampleApp** e selecione **projeto Freestyle**. Em seguida, selecione **OK**.

    ![Criar um novo trabalho](./media/install-solution-template-steps/jenkins-new-job.png) 

1. Selecione a guia **Gerenciamento de código-fonte**, habilite o **Git**e digite a seguinte URL no campo **URL do Repositório**:`https://github.com/spring-guides/gs-spring-boot.git`

    ![Definir o repositório Git](./media/install-solution-template-steps/jenkins-job-git-configuration.png) 

1. Selecione a guia **Build** e selecione **Adicionar etapa de compilação**, **Invocar script Gradle**. Selecione **Usar Wrapper Gradle**, digite `complete` na **localização do Wrapper** e `build` como **Tarefas**.

    ![Use o wrapper Gradle para compilar](./media/install-solution-template-steps/jenkins-job-gradle-config.png) 

1. Selecione **Avançado** e insira `complete` no campo **Script de Compilação da Raiz**. Clique em **Salvar**.

    ![Definir as configurações avançadas na etapa de compilação do wrapper Gradle](./media/install-solution-template-steps/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Compilar o código

1. Selecione **Criar agora** para compilar o código e empacotar o aplicativo de exemplo. Quando a compilação for concluída, selecione o link **Workspace** para o projeto.

    ![Navegue até o workspace para obter o arquivo JAR da compilação](./media/install-solution-template-steps/jenkins-access-workspace.png) 

1. Navegue até `complete/build/libs` e verifique se o `gs-spring-boot-0.1.0.jar` está disponível a fim de verificar se a compilação foi bem-sucedida. O servidor Jenkins agora está pronto para criar seus próprios projetos no Azure.

## <a name="troubleshooting-the-jenkins-solution-template"></a>Solução de problemas do modelo de solução do Jenkins

Caso encontre bugs com o modelo de solução do Jenkins, registre um problema no [repositório GitHub do Jenkins](https://github.com/azure/jenkins/issues).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Jenkins no Azure](/azure/developer/jenkins)