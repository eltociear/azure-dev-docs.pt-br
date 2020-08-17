---
title: 'Tutorial: Migrar um cluster do WebLogic Server para o Azure com o Gateway de Aplicativo do Azure como um balanceador de carga'
description: Este tutorial orienta você pela implantação do WebLogic Server no Azure com o Gateway de Aplicativo do Azure como um balanceador de carga
author: edburns
ms.author: edburns
ms.topic: tutorial
ms.date: 08/05/2020
ms.openlocfilehash: 166e6f90218eb519242da0d89ae6146a2d589863
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057531"
---
# <a name="tutorial-migrate-a-weblogic-server-cluster-to-azure-with-azure-application-gateway-as-a-load-balancer"></a>Tutorial: Migrar um cluster do WebLogic Server para o Azure com o Gateway de Aplicativo do Azure como um balanceador de carga

Este tutorial orienta você pelo processo de implantação do WLS (WebLogic Server) com o Gateway de Aplicativo do Azure.  Ele aborda as etapas específicas para criar um Key Vault, armazenar um certificado SSL no Key Vault e usar esse certificado para terminação SSL.  Embora todos esses elementos estejam bem documentados por si sós, este tutorial mostra a maneira específica em que todos esses elementos se reúnem para criar uma solução de balanceamento de carga simples e avançada para o WLS no Azure.

:::image type="content" border="false" source="media/migrate-weblogic-with-app-gateway/weblogic-app-gateway-key-vault.png" alt-text="Diagrama que mostra a relação entre o WLS, o Gateway de Aplicativo e o Key Vault.":::

O balanceamento de carga é uma parte essencial da migração do Cluster do Oracle WebLogic Server para o Azure.  A solução mais fácil é usar o suporte interno para o [Gateway de Aplicativo do Azure](/azure/application-gateway/overview).  O Gateway de Aplicativo está incluído como parte do suporte ao Cluster do WebLogic no Azure.  Para obter uma visão geral do suporte ao Cluster do WebLogic no Azure, confira [O que é o Oracle WebLogic Server no Azure?](/azure/virtual-machines/workloads/oracle/oracle-weblogic).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um Cofre de chaves do Azure
> * Criar um certificado SSL
> * Armazenar o certificado SSL no Key Vault
> * Implantar o WebLogic Server com o Gateway de Aplicativo do Azure no Azure
> * Validar a implantação bem-sucedida do WLS e do Gateway de Aplicativo

## <a name="prerequisites"></a>Pré-requisitos

* [OpenSSL](https://www.openssl.org/) em um computador que executa um ambiente de linha de comando de tipo UNIX.

   Embora possa haver outras ferramentas disponíveis para o gerenciamento de certificados, este tutorial usa OpenSSL. Você encontra o OpenSSL agrupado com várias distribuições do GNU/Linux, como o Ubuntu.
* Uma assinatura ativa do Azure.
  * Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).
* A capacidade de implantar um dos Aplicativos Azure do WLS listados em [Aplicativos Azure do Oracle WebLogic Server](/azure/virtual-machines/workloads/oracle/oracle-weblogic).

## <a name="migration-context"></a>Contexto de migração

Aqui estão alguns itens a serem considerados sobre a migração de instalações locais do WLS e do Gateway de Aplicativo do Azure.  Embora as etapas deste tutorial sejam a maneira mais fácil de estabelecer um balanceador de carga na frente do Cluster do WebLogic Server no Azure, há muitas outras maneiras de fazer isso.  Essa lista mostra algumas outras coisas a considerar.

* Se você tiver uma solução de balanceamento de carga, verifique se as funcionalidades dela são atendidas ou excedidas pelo Gateway de Aplicativo do Azure.  Para obter um resumo das funcionalidades do Gateway de Aplicativo do Azure em comparação com outras soluções de balanceamento de carga do Azure, confira [Visão geral das opções de balanceamento de carga no Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).
* Se sua solução de balanceamento de carga existente fornecer proteção de segurança contra explorações e vulnerabilidades comuns, o Gateway de Aplicativo atenderá às suas necessidades. O WAF (firewall do aplicativo Web) do Gateway de Aplicativo implementa os [conjuntos de regras principais do OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project).  Para obter mais informações sobre o suporte do WAF no Gateway de Aplicativo, confira [Recursos do Gateway de Aplicativo](/azure/application-gateway/features#web-application-firewall).
* Se sua solução de balanceamento de carga existente exigir criptografia SSL de ponta a ponta, você precisará fazer configurações adicionais depois de seguir as etapas neste guia.  Confira [Visão geral da terminação TLS e TLS de ponta a ponta com um Gateway de Aplicativo](/azure/application-gateway/ssl-overview#end-to-end-tls-encryption) e a documentação do Oracle sobre [Como configurar o SSL no Oracle Fusion Middleware](https://docs.oracle.com/en/middleware/fusion-middleware/12.2.1.3/asadm/configuring-ssl1.html#GUID-623906C0-B1FD-423F-AE51-061B5800E927).
* Se você estiver otimizando para a nuvem, este guia mostrará como começar do zero com o Gateway de Aplicativo do Azure e o WLS.
* Para uma pesquisa abrangente da migração do WebLogic Server para máquinas virtuais do Azure, confira [Migrar aplicativos do WebLogic Server para Máquinas Virtuais do Azure](migrate-weblogic-to-virtual-machines.md).

## <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure

Esta seção mostra como usar o portal do Azure para criar um Azure Key Vault.

1. No menu do portal do Azure ou na página **Início**, selecione **Criar um recurso**.
1. Digite **Key Vault** na caixa Pesquisar.
1. Na lista de resultados, escolha **Key Vault**.
1. Na seção Key Vault, escolha **Criar**.
1. A seção **Criar cofre de chaves** fornece as seguintes informações:
    * **Assinatura**: Escolha uma assinatura.
    * Em **Grupo de Recursos**, escolha **Criar** e digite um nome para o grupo de recursos.  Anote o nome do cofre de chaves.  *Você precisará dele mais tarde ao implantar o WLS.*
    * **Nome do Key Vault**: um nome exclusivo é necessário.  Anote o nome do cofre de chaves.  *Você precisará dele mais tarde ao implantar o WLS.*
    > [!NOTE]
    > Você pode usar o mesmo nome para **Grupo de recursos** e **Nome do cofre de chaves**.
    * No menu suspenso **Local**, escolha um local.
    * Deixe as outras opções em seus padrões.
1. Selecione **Avançar: Política de Acesso**.
1. Em **Habilitar acesso a**, selecione **Azure Resource Manager para implantação de modelo**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

A criação do cofre de chaves é razoavelmente leve, normalmente concluída em menos de dois minutos.  Quando a implantação for concluída, selecione **Ir para o recurso** e prossiga para a próxima seção.

## <a name="create-an-ssl-certificate"></a>Criar um certificado SSL

Esta seção mostra como criar um certificado SSL autoassinado em um formato adequado para uso pelo Gateway de Aplicativo implantado com o WebLogic no Azure.  O certificado precisa ter uma senha não vazia.  Se você já tiver um certificado SSL com senha válida e não vazia no formato *.pfx*, poderá ignorar esta seção e passar para a próxima.  Se o certificado SSL com uma senha existente, válida e não vazia não estiver no formato *.pfx*, primeiro converta-o em um arquivo *.pfx* antes de passar para a próxima seção.  Caso contrário, abra um shell de comando e digite os comandos a seguir.

> [!NOTE]
> Esta seção mostra como criptografar o certificado com codificação de base 64 antes de armazená-lo como um segredo no Key Vault.  Isso é exigido pela implantação subjacente do Azure que cria o WebLogic Server e o Gateway de Aplicativo.

Siga estas etapas para criar o certificado e codificá-lo em base 64:

1. Criar uma `RSA PRIVATE KEY`

   ```bash
   openssl genrsa 2048 > private.pem
   ```
1. Crie uma chave pública correspondente.

   ```bash
   openssl req -x509 -new -key private.pem -out public.pem
   ```

   Você terá que responder várias perguntas quando solicitado pela ferramenta OpenSSL.  Esses valores serão incluídos no certificado.  Este tutorial usa um certificado autoassinado, portanto, os valores são irrelevantes.  Os valores literais a seguir são aceitáveis.
     1. Para **Nome do País**, insira um código de duas letras.
     1. Para **Nome do Estado ou Província**, insira WA.
     1. Para **Nome da Organização**, insira Contoso.  Para Nome da Unidade Organizacional, insira cobrança.
     1. Para **Nome Comum**, insira Contoso.
     1. Para **Endereço de Email**, insira billing@contoso.com.

1. Exportar o certificado como um arquivo *.pfx*

   ```bash
   openssl pkcs12 -export -in public.pem -inkey private.pem -out mycert.pfx
   ```

   Digite a senha duas vezes.  Anote a senha.  *Você precisará dele mais tarde ao implantar o WLS.*

1. Codificar o arquivo *mycert.pfx* em base 64

   ```bash
   base64 mycert.pfx > mycert.txt
   ```

Agora que você criou um Key Vault e tem um certificado SSL válido com uma senha não vazia, pode armazenar o certificado no Key Vault.

## <a name="store-the-ssl-certificate-in-the-key-vault"></a>Armazenar o certificado SSL no Key Vault

Esta seção mostra como armazenar o certificado e a respectiva senha no Key Vault criado nas seções anteriores.

Para armazenar o certificado, siga estas etapas:

1. No portal do Azure, coloque o cursor na barra de pesquisa na parte superior da página e digite o nome do Key Vault que você criou anteriormente no tutorial.
1. Seu Key Vault deve aparecer no título **Recursos**.  Selecione-a.
1. Na seção **Configurações**, selecione **Segredos**.
1. Selecione **Gerar/Importar**.
1. Em **Opções de upload**, mantenha o valor padrão.
1. Em **Nome**, insira `myCertSecretData` ou qualquer nome que desejar.
1. Em **Valor**, insira o conteúdo do arquivo *mycert.txt*.  O comprimento do valor e a presença de caracteres de nova linha não são um problema para o campo de texto.
1. Mantenha os valores restantes com os respectivos padrões e selecione **Criar**.

Para armazenar a senha do certificado, siga estas etapas:

1. Você retornará à página **Segredos**.  Selecione **Gerar/Importar**.
1. Em **Opções de upload**, mantenha o valor padrão.
1. Em **Nome**, insira `myCertSecretPassword` ou qualquer nome que desejar.
1. Em **Valor**, insira a senha do certificado.
1. Mantenha os valores restantes com os respectivos padrões e selecione **Criar**.
1. Você retornará à página **Segredos**.

## <a name="deploy-weblogic-server-with-application-gateway-to-azure"></a>Implantar o WebLogic Server com o Gateway de Aplicativo no Azure

Esta seção mostrará a você como usar o Key Vault, o certificado SSL e a senha criados nas seções anteriores.  Você provisionará um cluster do WLS com um Gateway de Aplicativo do Azure criado automaticamente como o balanceador de carga para os nós de cluster.  O Gateway de Aplicativo usará o certificado SSL fornecido para a terminação SSL.  Para obter detalhes avançados sobre a terminação SSL com o Gateway de Aplicativo, confira [Visão geral da terminação TLS e do TLS de ponta a ponta com o Gateway de Aplicativo](/azure/application-gateway/ssl-overview).

Para criar o cluster do WLS e o Gateway de Aplicativo, siga estas etapas:

1. Comece seguindo as etapas para provisionar um Cluster do WebLogic Server, conforme descrito [na documentação do Oracle](https://aka.ms/arm-oraclelinux-wls-cluster-oracle-docs), mas retorne para esta página quando chegar à folha **Gateway de Aplicativo do Azure**, mostrada aqui.

   :::image type="content" source="media/migrate-weblogic-with-app-gateway/weblogic-app-gateway-blade.png" alt-text="Diagrama que mostra a relação entre o WLS, o Gateway de Aplicativo e o Key Vault.":::

1. Na folha **Gateway de Aplicativo do Azure**, selecione **Sim**.
1. Em **Nome do grupo de recursos na assinatura atual que contém o KeyVault**, insira o nome do grupo de recursos que contém o Key Vault criado anteriormente.
1. Em **Nome do Azure KeyVault que contém os segredos para o Certificado para Terminação SSL**, insira o nome do Key Vault.
1. Em **O nome do segredo no KeyVault especificado cujo valor corresponde aos dados do certificado SSL**, digite `myCertSecretData` ou qualquer nome que você tenha inserido anteriormente.
1. Em **O nome do segredo no KeyVault especificado cujo valor corresponde à senha do certificado SSL**, digite `myCertSecretData` ou qualquer nome que você tenha inserido anteriormente.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.  Isso fará uma validação da possibilidade de obter o certificado do Key Vault e de que a senha desse certificado corresponde ao valor que você armazenou para a senha no Key Vault.  Se essa etapa de validação falhar, examine as propriedades do Key Vault e verifique se o certificado e a senha foram inseridos corretamente.
1. Quando você vir **Validação aprovada**, selecione **Criar**.

Isso iniciará o processo de criação do Cluster do WLS e do respectivo Gateway de Aplicativo de front-end, o que pode levar cerca de 15 minutos.  Após a conclusão da implantação, selecione **Ir para o grupo de recursos**. Na lista de recursos no grupo de recursos, selecione **myAppGateway**.

## <a name="validate-successful-deployment-of-wls-and-app-gateway"></a>Validar a implantação bem-sucedida do WLS e do Gateway de Aplicativo

Esta seção mostra uma técnica para validar rapidamente a implantação bem-sucedida do cluster do WLS e do Gateway de Aplicativo.

Se você tinha selecionado **Ir para o grupo de recursos** e, em seguida, **myAppGateway** no final da seção anterior, estará vendo agora uma página de visão geral do Gateway de Aplicativo.  Caso contrário, você poderá encontrar essa página digitando `myAppGateway` na caixa de texto na parte superior do portal do Azure e, em seguida, selecionando a página correta que aparece.  Lembre-se de selecionar aquela no grupo de recursos que você criou para o cluster do WLS.  Em seguida, conclua as etapas abaixo.

1. No painel esquerdo da página de visão geral para **myAppGateway**, role para baixo até a seção **Monitoramento** e selecione **Integridade de back-end**.
1. Depois que a mensagem **carregando** desaparecer, você deverá ver uma tabela no meio da tela mostrando os nós do cluster configurados como nós no pool de back-end.
1. Verifique se o status mostra **Íntegro** para cada nó.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não for continuar a usar este cluster do WLS, exclua o Key Vault e o cluster do WLS seguindo estas etapas:

1. Visite a página de visão geral para **myAppGateway**, conforme mostrado na seção anterior.
1. Na parte superior da página, sob o texto **Grupo de recursos**, selecione o grupo de recursos.
1. Selecione **Excluir grupo de recursos**.
1. O foco de entrada será definido para o campo rotulado **DIGITE O NOME DO GRUPO DE RECURSOS**.  Digite o nome do grupo de recursos conforme solicitado.
1. Isso fará com que o botão **Excluir** seja habilitado.  Selecione o botão **Excluir**.  Essa operação levará algum tempo, mas você poderá continuar para a próxima etapa enquanto a exclusão estiver sendo processada.
1. Localize o Key Vault seguindo a primeira etapa da seção [Armazenar o certificado SSL no Key Vault]().
1. Selecione **Excluir**.
1. Selecione **Excluir** no painel que aparece.

## <a name="next-steps"></a>Próximas etapas

Continue a explorar as opções para executar o WLS no Azure.
> [!div class="nextstepaction"]
> [Saiba mais sobre o Oracle WebLogic no Azure](/azure/virtual-machines/workloads/oracle/oracle-weblogic)
