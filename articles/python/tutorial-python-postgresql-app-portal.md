---
title: 'Tutorial: Implantar um aplicativo Django com PostgreSQL usando o portal do Azure'
description: Provisione um aplicativo Web e um banco de dados PostgreSQL no Azure e implante o código do aplicativo do GitHub.
ms.devlang: python
ms.topic: tutorial
ms.date: 07/23/2020
ms.custom: devx-track-python
ms.openlocfilehash: 19c0dda48b0fb7b5b0c3af75a1d94d3c9b9e4080
ms.sourcegitcommit: 4824cea71195b188b4e8036746f58bf8b70dc224
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89753763"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-using-the-azure-portal"></a>Tutorial: Implantar um aplicativo Web Django com PostgreSQL usando o portal do Azure

Usando o portal do Azure, é possível implantar um aplicativo Web [Django](https://www.djangoproject.com/), escrito em Python, controlado por dados no [Serviço de Aplicativo do Azure](/azure/app-service/overview#app-service-on-linux) e conectá-lo a um [Banco de Dados do Azure para PostgreSQL](/azure/postgresql/). Você pode começar com um tipo de preço gratuito que pode ser escalado verticalmente depois.

O código do aplicativo Web, nesse caso, vem de um repositório GitHub, e você configura o aplicativo Web para implantação contínua do GitHub. Uma vez configurado, você pode fazer mais desenvolvimento no computador local e confirmar as alterações no repositório. O aplicativo Web no Azure implanta essas alterações automaticamente.

Neste tutorial, você usa o portal do Azure para concluir as seguintes tarefas:

> [!div class="checklist"]
> - Provisionar um aplicativo Web no Azure que é implantado de um repositório GitHub
> - Provisionar um servidor e banco de dados PostgreSQL no Azure e conectá-los ao aplicativo Web.
> - Atualizar seu código e confirmar as alterações para reimplantar automaticamente do GitHub.
> - Exibir logs de diagnóstico
> - Gerenciar o aplicativo Web no portal do Azure

Você também pode usar a **[versão deste tutorial baseada na CLI do Azure](/azure/app-service/tutorial-python-postgresql-app)** .

## <a name="fork-the-sample-repository"></a>Bifurcar a amostra de repositório

Em um navegador, acesse [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) e crie fork do repositório na sua conta do GitHub.

Você cria um fork desse repositório para que possa fazer alterações e reimplantar o código em uma etapa posterior.

**(Opcional) Sobre o exemplo:** O exemplo djangoapp contém o aplicativo de enquetes do Django, controlado por dados, que você obtém seguindo [Escrever seu primeiro aplicativo Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) na documentação do Django. O exemplo também é modificado usando a [lista de verificação de implantação do Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/) para execução em um ambiente de produção como o Serviço de Aplicativo do Azure. (Essas alterações são para qualquer ambiente de produção e não são específicas para o Azure.)

- As configurações de produção estão no arquivo *azuresite/production.py*. Os detalhes de desenvolvimento estão em *azuresite/settings.py*.

- O aplicativo usa as configurações de produção quando a variável de ambiente `DJANGO_ENV` é definida como "produção". Você criará essa variável de ambiente posteriormente no tutorial junto com outras usadas para a configuração do banco de dados PostgreSQL.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="provision-the-web-app-in-azure"></a>Provisionar o aplicativo Web no Azure

1. Abra o [portal do Azure](https://portal.azure.com)

1. Selecione **Criar um recurso**.

1. Na página **Novo**, selecione **Aplicativo Web** na coluna **Popular**. (Se você não vir o **Aplicativo Web** imediatamente, selecione **Web** em **Azure Marketplace** e, em seguida, selecione **Aplicativo Web** em **Em destaque**.) 

1. Na página **Criar Aplicativo Web**, insira as seguintes informações:

    | Campo | Valor |
    | --- | --- |
    | Subscription | Selecione a assinatura que você deseja usar se for diferente do padrão. |
    | Resource group | Selecione **Criar** e insira "DjangoPostgres-Tutorial-rg". |
    | Nome do aplicativo | Um nome para seu aplicativo Web que é exclusivo em todo o Azure (a URL do aplicativo é `https://\<app-name>.azurewebsites.net`). Os caracteres permitidos são `A`-`Z`, `0`-`9` e `-`. Um bom padrão é usar uma combinação do nome da empresa e um identificador de aplicativo. |
    | Publicar | Selecione **Código**. |
    | Pilha de runtime | Selecione **Python 3.8** na lista suspensa. |
    | Região | Selecione uma localização próxima de você. |
    | Plano do Linux | O portal preencherá esse campo com um nome de Plano do Serviço de Aplicativo com base em seu grupo de recursos. Se você quiser alterar o nome, selecione **Criar**. |
    | SKU e tamanho | Para obter o melhor desempenho, use o plano padrão, embora incorra em encargos em sua assinatura. Para evitar encargos, selecione **Alterar tamanho**, em seguida, selecione **Desenvolvimento/Teste**, selecione **B1** (gratuito por 30 dias) e, em seguida, selecione **Aplicar**. Você pode escalar o plano posteriormente para melhorar o desempenho. |

1. Selecione **Examinar + Criar** e **Criar**. O Azure leva alguns minutos para provisionar o aplicativo Web.

1. Após a conclusão do provisionamento, selecione **Ir para o recurso** para abrir a página de visão geral do aplicativo Web. Mantenha esta janela ou guia do navegador aberta para as etapas posteriores.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="provision-the-postgresql-database-server-in-azure"></a>Provisionar o servidor de banco de dados PostgreSQL no Azure

1. Abra uma nova janela ou guia do navegador com o [portal do Azure](https://portal.azure.com). Use uma nova guia para provisionar o banco de dados, pois você precisará transferir algumas informações da página do banco de dados para a página do aplicativo Web ainda aberta na seção anterior.

1. Selecione **Criar um recurso**.

1. Na página **Novo**, selecione **Banco de dados** em **Azure Marketplace** e, em seguida, selecione **Banco de Dados do Azure para PostgreSQL** em **Em destaque**.)

1. Na página seguinte, selecione **Criar** em **Servidor único**.

1. Na página **Servidor único**, insira as seguintes informações:

    | Campo | Valor |
    | --- | --- |
    | Subscription | Selecione a assinatura que você deseja usar se for diferente do padrão. |
    | Resource group | Selecione o grupo "DjangoPostgres-Tutorial-rg" criado na seção anterior. |
    | Nome do servidor | Um nome para seu servidor de banco de dados que é exclusivo em todo o Azure (a URL do aplicativo é `https://\<server-name>.postgres.database.azure.com`). Os caracteres permitidos são `A`-`Z`, `0`-`9` e `-`. Um bom padrão é usar uma combinação do nome da empresa e um identificador do servidor. |
    | Fonte de dados | **Nenhuma** |
    | Location | Selecione uma localização próxima de você. |
    | Versão | Mantenha o padrão (que é a versão mais recente). |
    | Computação + armazenamento | Selecione **Configurar servidor** e, em seguida, selecione **Básico** e **Geração 5**. Defina **vCore** como 1, defina **Armazenamento** como 5 GB e, em seguida, selecione **OK**. Essas opções provisionam o servidor menos dispendioso disponível para PostgreSQL no Azure. |
    | Nome de usuário administrador, Senha, Confirmar senha | Insira as credenciais para uma conta de administrador no servidor de banco de dados. Registre essas credenciais, pois você precisará delas mais tarde neste tutorial. |

1. Selecione **Examinar + Criar** e **Criar**. O Azure leva alguns minutos para provisionar o aplicativo Web.

1. Após a conclusão do provisionamento, selecione **Ir para o recurso** para abrir a página de visão geral do servidor de banco de dados.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="create-the-pollsdb-database-on-the-postgresql-server"></a>Criar o banco de dados pollsdb no servidor PostgreSQL

Nesta seção, você se conecta ao servidor de banco de dados no Azure Cloud Shell e usa um comando PostgreSQL para criar um banco de dados "pollsdb" no servidor. Esse banco de dados é esperado pelo código do aplicativo de exemplo.

1. Na página de visão geral do servidor PostgreSQL, selecione **Segurança da conexão** (em **Configurações** no lado esquerdo).

    ![Página de segurança da conexão do portal para regras de firewall](media/tutorial-python-postgresql-app-portal/server-firewall-rules.png)

1. Selecione o botão rotulado **Adicionar 0.0.0.0 – 255.255.255.255** e, em seguida, selecione **Continuar** na mensagem pop-up exibida, seguida por **Salvar** na parte superior da página. Essas ações adicionam uma regra que permite que você se conecte ao servidor de banco de dados do Cloud Shell, bem como SSH (assim como você fará em uma seção posterior para executar migrações de modelo de dados do Django).

1. Abra o Azure Cloud Shell no portal do Azure selecionando o ícone do Cloud Shell na parte superior da janela:

    ![Botão Cloud Shell na barra de ferramentas do portal do Azure](media/tutorial-python-postgresql-app-portal/portal-launch-icon.png)

1. No Cloud Shell, execute o seguinte comando:

    ```bash
    psql --host=<server-name>.postgres.database.azure.com --port=5432 --username=<user-name>@<server-name> --dbname=postgres
    ```

    Substitua `<server-name>` e `<user-name>` pelos nomes usados na seção anterior ao configurar o servidor. Observe que o valor de nome de usuário completo é `<user-name>@<server-name>`.

    Você pode copiar o comando acima e colar no Cloud Shell usando um clique com o botão direito do mouse e, em seguida, selecionando **Colar**.

    Insira a senha de administrador quando solicitado.

1. Quando o shell se conectar com êxito, você deverá ver o prompt `postgres=>`. Esse prompt indica que você está conectado ao banco de dados administrativo padrão chamado "postgres". (O banco de dados "postgres" não se destina ao uso do aplicativo.)

1. No prompt, execute o comando `CREATE DATABASE pollsdb;`. Certifique-se de incluir o ponto e vírgula final, que conclui o comando.

1. Se o banco de dados for criado com êxito, o comando deverá exibir `CREATE DATABASE`. Para verificar a data de criação do banco de dados, execute `\c pollsdb`. Esse comando deve alterar o prompt para `pollsdb=>`, que indica êxito.

1. Saia do psql executando o comando `exit`.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="connect-the-database"></a>Conectar o banco de dados

Nesta seção, você cria as configurações que o aplicativo Web precisa para se conectar ao banco de dados do `pollsdb`. Essas configurações aparecem no código do aplicativo como variáveis de ambiente. (Para obter mais informações, confira [Acessar variáveis do ambiente](/azure/app-service/configure-language-python#access-environment-variables).)

1. Alterne para a guia ou janela do navegador para o aplicativo Web que você criou em uma seção anterior.

1. Selecione **Configuração** (em **Configurações** no lado esquerdo) e, em seguida, selecione **Configurações do aplicativo** na parte superior da página.

    ![Definição das configurações do portal para aplicativos Web](media/tutorial-python-postgresql-app-portal/web-app-settings.png)

1. Use o botão **Nova configuração de aplicativo** para criar configurações para cada um dos seguintes valores:

    | Nome da configuração | Valor |
    | --- | --- |
    | DJANGO_ENV | `production` (Esse valor informa ao aplicativo para usar uma configuração de produção, conforme descrito anteriormente na [visão geral de exemplo](#fork-the-sample-repository).) |
    | DBHOST | A URL do servidor de banco de dados da seção anterior, no formato `<server-name>.postgres.database.azure.com`. Você pode copiar a URL inteira da página de visão geral do servidor de banco de dados. |
    | DBNAME | `pollsdb` |
    | DBUSER | O nome de usuário administrador completo como usado na seção anterior. O nome de usuário completo é novamente `<user-name>@<server-name>`. |
    | DBPASS | A senha de administrador que você criou anteriormente. |

1. Selecione **Salvar** e, em seguida, **Continuar** para aplicar as configurações.

    > [!IMPORTANT]
    > Selecionar **Salvar** depois de fazer alterações nas configurações é essencial. As configurações que você criar com o botão **Nova configuração de aplicativo** não serão aplicadas até que você use a opção **Salvar**.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="deploy-app-code-to-the-web-app-from-a-repository"></a>Implantar o código do aplicativo no aplicativo Web de um repositório

Com as configurações de banco de dados e conexão em vigor, agora você pode configurar o aplicativo Web para implantar o código diretamente de um repositório GitHub.

1. Na guia ou janela do navegador do aplicativo Web, selecione **Centro de Implantação** (em **Implantação** no lado esquerdo).

1. Na etapa **Controle do Código-fonte**, selecione **GitHub**, selecione Seguir prompts de entrada ou selecione **Continuar** para usar o logon atual do GitHub.

1. Na etapa **Provedor de Build**, selecione **Serviço de build do Serviço de Aplicativo** e, em seguida, selecione **Continuar**.

1. Na etapa **Configurar**, selecione os seguintes valores:

    | Campo | Valor |
    | --- | --- |
    | Organização | A conta do GitHub para a qual você criou fork do repositório de exemplo. |
    | Repositório | djangoapp |
    | Branch | master |

1. Selecione **Continuar** para selecionar o repositório e, em seguida, selecione **Concluir**. O Azure deve implantar o código em alguns segundos e iniciar o aplicativo.

    O Serviço de Aplicativo detecta um projeto Django procurando um arquivo *wsgi.py* em cada subpasta. Quando o Serviço de Aplicativo encontra o arquivo, ele carrega o aplicativo Web Django. Para obter mais informações, confira [Configurar imagem interna do Python](/azure/app-service/configure-language-python).

[Está com problemas? Fale conosco.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="run-django-database-migrations"></a>Executar migrações de banco de dados do Django

Com o código implantado e o banco de dados em vigor, o aplicativo está quase pronto para uso. A única parte que resta é estabelecer o esquema necessário no próprio banco de dados. Isso é feito por meio da "migração" dos modelos de dados no aplicativo Django para o banco de dados.

1. Na janela do navegador ou na guia do aplicativo Web, selecione **SSH** (em **Ferramentas de Desenvolvimento** no lado esquerdo) e **Ir** para abrir um console do SSH no servidor de aplicativo Web. Pode levar um minuto para se conectar pela primeira vez, pois o contêiner do aplicativo Web precisa ser iniciado.

1. No console, altere para a pasta do aplicativo Web:

    ```bash
    cd site/wwwroot
    ```

1. Ative o ambiente virtual do contêiner:

    ```bash
    source /antenv/bin/activate
    ```

1. Instale os pacotes do Python:

    ```bash
    pip install -r requirements.txt
    ```

1. Execute as migrações de banco de dados:

    ```bash
    python manage.py migrate
    ```

1. Crie um logon de administrador para o aplicativo:

    ```bash
    python manage.py createsuperuser
   ```

    O comando `createsuperuser` solicita as credenciais de superusuário (ou administrador) do Django, que são usadas no aplicativo Web. Para este tutorial, use o nome de usuário padrão `root`, pressione **Enter** para o endereço de email ser deixado em branco e digite `Pollsdb1` para a senha.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoPortalTutorialHelp)

### <a name="create-a-poll-question-in-the-app"></a>Criar uma pergunta de enquete no aplicativo

Agora você está pronto para executar um teste rápido do aplicativo para demonstrar que ele está funcionando com o banco de dados PostgreSQL.

1. Na guia ou janela do navegador do aplicativo Web, volte à página **Visão geral** e, em seguida, selecione a **URL** para o aplicativo Web (do formulário `http://<app-name>.azurewebsites.net`).

1. O aplicativo deve exibir a mensagem "Não há enquetes disponíveis" porque ainda não há enquetes específicas no banco de dados.

1. Navegue até `http://<app-name>.azurewebsites.net/admin` (a página "Administração do Django") e entre usando as credenciais de superusuário da seção anterior (`root` e `Pollsdb1`).

1. Em **Enquetes**, selecione **Adicionar** ao lado de **Perguntas** e crie uma enquete com algumas opções.

1. Navegue novamente para `http://<app-name>.azurewebsites.net/` para confirmar que as perguntas agora são apresentadas ao usuário. Responda às perguntas como desejar para gerar dados no banco de dados.

**Parabéns!** Você está executando um aplicativo Web Django, escrito em Python, no Serviço de Aplicativo do Azure para Linux, com um banco de dados PostgreSQL ativo.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="update-the-app-and-redeploy"></a>Atualizar o aplicativo e reimplantar

Conforme descrito anteriormente neste tutorial, o Azure reimplanta o código do aplicativo sempre que você confirma as alterações no repositório do GitHub.

No entanto, se você alterar os modelos de dados do aplicativo Django, deverá migrar essas alterações para o banco de dados:

1. Conecte-se ao aplicativo Web novamente via SSH, conforme descrito em [Executar migrações de banco de dados do Django](#run-django-database-migrations).

1. Faça a alteração na pasta do aplicativo com `cd site/wwwroot`.

1. Ative o ambiente virtual com `source /antenv/bin/activate`.

1. Execute as migrações novamente com `python manage.py migrate`.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="view-diagnostic-logs"></a>Exibir logs de diagnóstico

Você pode acessar os logs do console gerados de dentro do contêiner que hospeda o aplicativo no Azure.

Na página do aplicativo Web no portal do Azure, selecione **Fluxo de log** (em **Monitoramento** no lado esquerdo). Os logs aparecem como saída do console.

Você também pode inspecionar os arquivos de log do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode deixar o aplicativo e o banco de dados em execução desde que queira mais trabalho de desenvolvimento. Caso contrário, para evitar incorrer em encargos contínuos, exclua a criação do grupo de recursos para este tutorial, que exclui todos os recursos contidos nele:

1. No portal do Azure, digite "DjangoPostgres-Tutorial-rg" na barra de pesquisa na parte superior da janela e, em seguida, selecione o mesmo nome em **Grupos de Recursos**.

1. Na página Grupo de recursos, selecione **Excluir grupo de recursos**.

1. Quando solicitado, digite o nome do grupo de recursos e selecione **Excluir**.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="next-steps"></a>Próximas etapas

Saiba como o Serviço de Aplicativo executa um aplicativo Python:

> [!div class="nextstepaction"]
> [Configurar um aplicativo Python](/azure/app-service/configure-language-python)
