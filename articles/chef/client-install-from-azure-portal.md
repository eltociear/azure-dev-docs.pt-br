---
title: Instalar o cliente do Chef no portal do Azure
description: Saiba como implantar e configurar o cliente do Chef no portal do Azure
keywords: azure, chef, devops, cliente, instalar, portal
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: b5dd158bd06511bf440228d4ae0948596bca0612
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "80892895"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Instalar o cliente do Chef no portal do Azure
Você pode adicionar a extensão cliente da Chef diretamente em computador Linux ou Windows, por meio do portal do Azure. Este artigo o orienta pelo processo usando uma nova máquina virtual Linux.

## <a name="prerequisites"></a>Prerequisites

- **Assinatura do Azure**: caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Chef**: se você não tiver uma conta ativa do Chef, inscreva-se para [avaliação gratuita do Chef hospedado](https://manage.chef.io/signup). Para acompanhar as instruções neste artigo, você precisará dos seguintes valores de sua conta do Chef:
  - chave organization_validation
  - rb
  - run_list

## <a name="configure-a-new-virtual-machine-with-chef"></a>Configurar uma nova máquina virtual com o Chef

Nesta seção, primeiro você usará o portal do Azure para criar uma máquina Linux. Durante o processo, você também verá como instalar a extensão do Chef na nova máquina virtual.

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. No menu à esquerda, selecione a opção **Máquinas virtuais**. Se a opção **Máquinas Virtuais** não estive presente, selecione **Todos os serviços** e, em seguida, selecione **Máquinas virtuais**.

1. Na guia **Máquinas virtuais** guia, selecione **Adicionar**.

    ![Adicione uma nova máquina virtual usando o portal do Azure](./media/client-install-from-azure-portal/add-vm.png)

1. Na guia **Computação**, selecione o sistema operacional desejado. Para esta demonstração, o **Ubuntu Server** está selecionado.

1. Na guia **Ubuntu Server**, selecione **Ubuntu Server 16.04 LTS**.

    ![Ao criar uma máquina virtual de Ubuntu, especifique a versão necessária](./media/client-install-from-azure-portal/ubuntu-server-version.png)

1. Na guia **Ubuntu Server 16.04 LTS**, selecione **Criar**.

    ![O Ubuntu fornece informações adicionais sobre o produto](./media/client-install-from-azure-portal/create-vm.png)

1. Na guia **Criar a máquina virtual**, selecione **Básico**.

1. Na guia **Básico**, especifique os valores a seguir, e em seguida, selecione **OK**.

   - **Nome** - Insira um nome para a nova máquina virtual.
   - **Tipo de disco VM** - especifique o **SSD** ou **HDD** para o tipo de disco de armazenamento. Para saber mais sobre tipos de disco de máquina virtual no Azure, confira o artigo [Selecionar um tipo de disco](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types).
   - **Nome de Usuário** - Insira um nome de usuário que receberá privilégios de administrador na máquina virtual.
   - **Tipo de Autenticação**, Selecione **Senha**. Você também pode selecionar **chave pública SSH**e forneça um valor de chave pública SSH. Para fins desta demonstração (e nas capturas de tela), **Senha** é selecionada.
   - **Senha** e **Confirmar senha** - digite uma senha para o usuário.
   - **Logon com o Microsoft Azure Active Directory**  - Selecione **Desabilitado**.
   - **Assinatura** – selecione a assinatura do Azure desejada, se você tiver mais de uma.
   - **Grupo de recursos** - Insira um nome para seu grupo de recursos.
   - **Local** - Selecione **Leste dos EUA**.

     ![Guia básico para a criação de uma máquina virtual](./media/client-install-from-azure-portal/add-vm-basics.png)

1. Na guia **Escolher um tamanho**, selecione o tamanho para a máquina virtual, em seguida, selecione **Selecionar**.

1. Na guia **configurações**, a maioria dos valores será preenchido com base nos valores selecionados nas guias anteriores. Selecione **Extensões**.

     ![As extensões são adicionadas às máquinas virtuais por meio da guia Configurações](./media/client-install-from-azure-portal/add-vm-select-extensions.png)

1. Na guia **Extensões**, selecione **Adicionar extensão**.

     ![Selecione Adicionar extensão para adicionar uma extensão para uma máquina virtual](./media/client-install-from-azure-portal/add-vm-add-extension.png)

1. Na guia **Novo recurso** guia, selecione **extensão Chef do Linux (1.2.3)** .

     ![O Chef tem extensões para as máquinas virtuais Linux e Windows](./media/client-install-from-azure-portal/select-linux-chef-extension.png)

1. Na guia **Extensão Chef Linux**, selecione **Criar**.

1. Na guia **Instalar extensão**, especifique os valores a seguir, e em seguida, selecione **OK**.

    - **URL de servidor do Chef** – insira a URL de servidor do Chef que inclui o nome da organização, por exemplo, *https://api.chef.io/organization/mycompany* .
    - **Nome do nó do Chef** – insira o nome do nó do Chef.
    - **Lista de execução** – insira a lista de execução do Chef que é adicionado ao computador. Esse valor pode ser deixado em branco.
    - **Nome do cliente de validação** – insira o nome do cliente de validação do Chef. Por exemplo, `tarcher-validator`.
    - **Chave de validação** - selecione um arquivo que contém a chave de validação usada ao inicializar suas máquinas.
    - **Arquivo de configuração do cliente** – selecione um arquivo de configuração de cliente do Chef. Esse valor pode ser deixado em branco.
    - **Versão de cliente do Chef** - insira a versão do cliente para instalar o chef. Esse valor pode ser deixado em branco, o que instalará a versão mais recente.
    - **Modo de verificação de SSL** - selecione **Nenhum** ou **Par**. *Nenhum* foi selecionado para a demonstração.
    - **Ambiente do Chef** - inserir o ambiente do Chef neste nó deve ser um membro do. Esse valor pode ser deixado em branco.
    - **Segredo do recipiente de dados criptografados** – selecione um arquivo que contém o segredo para o recipiente de dados criptografados que este computador precisa acessar. Esse valor pode ser deixado em branco.
    - **Certificado SSL do servidor do Chef** – selecione o certificado SSL atribuído ao servidor do Chef. Esse valor pode ser deixado em branco.

      ![Instalando o servidor do Chef em uma máquina virtual Linux](./media/client-install-from-azure-portal/install-extension.png)

1. Quando a guia **Extensões** for exibida, selecione **OK**.

1. Quando a guia **Configurações** for exibida, selecione **OK**.

1. Quando a guia **Criar** for exibida, você verá um resumo das opções que selecionou e inseriu. Verifique as informações, bem como os **Termos de uso** e selecione **Criar**.

Quando o processo de criar e implantar a máquina virtual com a extensão do Chef estiver concluído, uma notificação indica o êxito ou falha da operação. Além disso, a página do recurso para a nova máquina virtual é aberta automaticamente no portal do Azure depois que é criada.

![Instalando o servidor do Chef em uma máquina virtual Linux](./media/client-install-from-azure-portal/resource-created.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Criar uma Máquina Virtual do Windows no Azure usando o Chef](windows-vm-configure.md)