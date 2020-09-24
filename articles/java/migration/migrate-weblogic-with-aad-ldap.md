---
title: Autorização e autenticação do usuário final com Azure Active Directory para migrar aplicativos Java no WebLogic Server para o Azure
description: Este guia descreve como configurar o Oracle WebLogic Server para se conectar ao Azure Active Directory Domain Services via LDAP
author: edburns
ms.author: edburns
ms.topic: tutorial
ms.date: 08/10/2020
ms.openlocfilehash: 2ee3e7c03d0fd7f7048037fb27f2cfacfe39671d
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831382"
---
# <a name="end-user-authorization-and-authentication-for-migrating-java-apps-on-weblogic-server-to-azure"></a>Autorização e autenticação do usuário final para migração de aplicativos Java no WebLogic Server para o Azure

Este guia ajudará você a habilitar a autenticação e a autorização do usuário final de nível corporativo para aplicativos Java no WebLogic Server usando o Azure Active Directory.

Os desenvolvedores de Java EE esperam que os [mecanismos de segurança da plataforma padrão](https://javaee.github.io/tutorial/security-intro.html#BNBWJ) "simplesmente funcionem", mesmo ao mover suas cargas de trabalho para o Azure.  Os [Aplicativos Azure do Oracle WLS (WebLogic Server)](/azure/virtual-machines/workloads/oracle/oracle-weblogic) permitem que você popule o realm de segurança interno com os usuários do Azure AD DS (Azure Active Directory Domain Services).  Use o elemento `<security-role>` padrão em seu Java EE em aplicativos do Azure. As informações do usuário fluem do Azure AD DS por meio do protocolo LDAP.

Este guia é dividido em duas partes.  Se você já tiver o Azure AD DS com o LDAP seguro exposto, poderá pular diretamente para a segunda parte.

* [Configuração do Azure Active Directory](#azure-active-directory-configuration)
* [Configuração do WLS](#wls-configuration)

Neste guia, você aprenderá a:

> [!div class="checklist"]
> * Criar e configurar um domínio gerenciado do Azure Active Directory Domain Services
> * Configurar o protocolo LDAP para um domínio gerenciado do Azure AD DS
> * Habilitar o WebLogic Server para acessar o LDAP como seu realm de segurança padrão

Este guia não ajuda você a reconfigurar uma implantação existente do Azure AD, mas deve ser possível acompanhar este guia e ver quais etapas podem ser ignoradas.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura ativa do Azure.
  * Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).
* A capacidade de implantar um dos Aplicativos Azure do WLS listados em [Aplicativos Azure do Oracle WebLogic Server](/azure/virtual-machines/workloads/oracle/oracle-weblogic).

## <a name="migration-context"></a>Contexto de migração

Aqui estão alguns itens a serem considerados sobre a migração de instalações locais do WLS e do Azure AD.

* Se você já tiver um locatário do Azure AD sem Serviços de Domínio expostos via LDAP, este guia mostrará como expor a funcionalidade LDAP e integrá-la ao WLS.
* Se o cenário envolver uma floresta local do Active Directory, considere implementar uma solução de identidade híbrida com o Azure AD.  Para obter mais informações, confira a [Documentação sobre identidade híbrida](/azure/active-directory/hybrid/)
* Se você já tiver a implantação do AD DS (Active Directory Domain Services) local, explore os caminhos de migração acessando [Comparar Active Directory Domain Services autogerenciado, Azure Active Directory e Azure Active Directory Domain Services gerenciado](/azure/active-directory-domain-services/compare-identity-solutions).
* Se você estiver otimizando para a nuvem, este guia mostrará como começar do zero com o LDAP do Azure AD DS e o WLS.
* Para uma pesquisa abrangente da migração do WebLogic Server para máquinas virtuais do Azure, confira [Migrar aplicativos do WebLogic Server para Máquinas Virtuais do Azure](migrate-weblogic-to-virtual-machines.md).

## <a name="azure-active-directory-configuration"></a>Configuração do Azure Active Directory

Esta seção orienta você por todas as etapas para criar uma instância de Azure AD DS integrada ao WLS.  O Azure Active Directory não dá suporte ao protocolo LDAP ou LDAP Seguro diretamente. Em vez disso, o suporte é habilitado por meio da instância do Azure AD DS (Azure AD Domain Services) em seu locatário do Azure AD.

>[!NOTE]
> Este guia usa o recurso de conta de usuário "somente nuvem" do Azure AD DS.  Outros tipos de conta de usuário têm suporte, mas não são descritos neste guia.

### <a name="create-and-configure-an-azure-active-directory-domain-services-managed-domain"></a>Criar e configurar um domínio gerenciado do Azure Active Directory Domain Services

Esta seção orienta você por um tutorial separado para criar um domínio gerenciado do Azure AD DS.

Conclua o tutorial [Criar e configurar um domínio gerenciado do Azure Active Directory Domain Services](/azure/active-directory-domain-services/tutorial-create-instance) até, mas não incluindo, a seção [Habilitar contas de usuário para o Azure AD DS](/azure/active-directory-domain-services/tutorial-create-instance#enable-user-accounts-for-azure-ad-ds).  Essa seção requer tratamento especial no contexto deste tutorial, conforme descrito na próxima seção.  Certifique-se de concluir as ações de DNS de modo completo e correto.

Anote o valor especificado ao concluir a etapa "Inserir um nome de domínio DNS para seu domínio gerenciado".  Você o usará posteriormente neste artigo.

### <a name="create-users-and-reset-passwords"></a>Criar usuários e redefinir senhas

Esta seção inclui etapas para criar usuários e alterar sua senha, que é necessária para fazer com que os usuários se propaguem com êxito por meio do LDAP.  Se você tiver uma instalação existente do Azure AD DS, essa etapa poderá não ser necessária.

1. No portal do Azure, verifique se o diretório correspondente ao locatário do Azure AD é o Active Directory atual.  Para saber como selecionar o diretório correto, confira [Associar ou adicionar uma assinatura do Azure ao seu locatário do Azure Active Directory](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory).  Se o diretório incorreto for selecionado, você não poderá criar usuários ou criará usuários no diretório errado.
1. Na caixa de pesquisa na parte superior do portal do Azure, insira "Usuários".
1. Selecione **Novo usuário**.
1. Verifique se **Criar usuário** está selecionado.
1. Preencha os valores para Nome de usuário, Nome e Sobrenome.  Deixe os outros campos com suas definições padrão.
1. Selecione **Criar**.
1. Selecione o usuário recém-criado na tabela.
1. Selecione **Redefinir senha**.
1. No painel que aparece, selecione **Redefinir senha**.
1. Anote a senha temporária.
1. Em uma janela anônima do navegador, acesse [o portal do Azure](https://portal.azure.com/) e faça logon com as credenciais e a senha do usuário.
1. Altere a senha, quando solicitado.  Anote a nova senha.  Você a usará mais tarde.
1. Faça logoff e feche a janela anônima.

Repita as etapas de "Selecionar **Novo usuário**" por meio de "Fazer logoff e fechar" para cada usuário que você deseja habilitar.

### <a name="allow-ldap-in-azure-ad-ds"></a>Permitir o LDAP no Azure AD DS

Esta seção orienta você por um tutorial separado para extrair valores para uso na configuração do WLS.

Primeiro, abra o tutorial [Configurar o LDAP seguro para um domínio gerenciado do Azure Active Directory Domain Services](/azure/active-directory-domain-services/tutorial-configure-ldaps) em uma janela separada do navegador para que você possa examinar as variações abaixo conforme executa o tutorial.  

Quando chegar à seção [Exportar um certificado para computadores cliente](/azure/active-directory-domain-services/tutorial-configure-ldaps#export-a-certificate-for-client-computers), anote o local no qual salvará o arquivo de certificado que termina em *.cer*.  Usaremos o certificado como entrada para a configuração do WLS.

Quando chegar à seção, [Bloquear o acesso LDAP seguro pela Internet](/azure/active-directory-domain-services/tutorial-configure-ldaps#lock-down-secure-ldap-access-over-the-internet), especifique **Qualquer** como a origem.  Fortaleceremos a regra de segurança com um endereço IP específico posteriormente neste guia.

Antes de executar as etapas em [Testar consultas para o domínio gerenciado](/azure/active-directory-domain-services/tutorial-configure-ldaps#test-queries-to-the-managed-domain), execute as etapas a seguir para habilitar o teste para ter sucesso.

   1. No portal, acesse a página de visão geral da instância do Azure AD Domain Services.
   1. Na área Configurações, selecione **Propriedades**.
   1. À direita da página, role para baixo até ver **Grupo admin**.  Abaixo desse título deve haver um link para **Administradores de DC do AAD**.  Selecione esse link.
   1. Na seção **Gerenciar**, selecione **Membros**.
   1. Selecione **Adicionar membros**.
   1. No campo de texto **Pesquisar**, insira alguns caracteres para localizar um dos usuários que você criou em uma etapa anterior.
   1. Selecione o usuário e ative o botão **Selecionar**.
   1. Esse usuário é o que você deve usar ao executar as etapas na seção **Testar consultas para o domínio gerenciado**.
   >[!NOTE]
   >
   > Aqui estão algumas dicas sobre como consultar os dados do LDAP, que você precisará fazer para coletar alguns valores necessários para a configuração do WLS.
   >
   > * O tutorial aconselha o uso do programa *LDP.exe* do Windows.  Esse programa está disponível apenas no Windows.  Para usuários não Windows, também é possível usar o [Apache Directory Studio](https://directory.apache.org/studio/downloads.html) para a mesma finalidade.
   > * Ao fazer logon no LDAP com *LDP.exe*, o nome de usuário será apenas a parte antes de @.  Por exemplo, se o usuário for `alice@contoso.onmicrosoft.com`, o nome de usuário para a ação de associação do *LDP.exe* será `alice`.  Além disso, deixe *LDP.exe* em execução e conectado para uso nas etapas subsequentes.
   >
Na seção [Configurar zona DNS para acesso externo](/azure/active-directory-domain-services/tutorial-configure-ldaps#configure-dns-zone-for-external-access), anote o valor do **Endereço IP externo do LDAP Seguro**.  Você o usará mais tarde.

Se o valor do **endereço IP externo do LDAP Seguro** não estiver prontamente aparente, siga estas etapas para obter o endereço IP.

1. No portal, localize o grupo de recursos que contém o recurso do Azure AD Domain Services.
1. Na lista de recursos, selecione o recurso de IP para o Azure AD Domain Services, conforme mostrado a seguir.  O IP provavelmente será iniciado com `aads`.
   :::image type="content" source="media/migrate-weblogic-with-aad-ldap/alternate-secure-ip-address-technique.png" alt-text="Navegador mostrando como selecionar o IP.":::
1. O IP é mostrado ao lado do rótulo, **endereço IP**.

Não execute as etapas em [Recursos de limpeza](/azure/active-directory-domain-services/tutorial-configure-ldaps#clean-up-resources) até receber uma solicitação para fazer isso neste guia.

Sabendo das variações acima, conclua [Configurar o LDAP Seguro para um domínio gerenciado do Azure Active Directory Domain Services](/azure/active-directory-domain-services/tutorial-configure-ldaps).  Agora podemos coletar os valores necessários para fornecer à configuração do WLS.

>[!NOTE]
> Aguarde até a conclusão do processamento da configuração do LDAP seguro antes de passar para a próxima seção.

### <a name="disable-weak-tls-v1"></a>Desabilitar a criptografia TLS v1 fraca

Por padrão, o Azure AD DS (Azure Active Directory Domain Services) permite o uso da TLS v1, que é considerada fraca e não é compatível com o WebLogic Server 14 e versões posteriores. 

Esta seção explica como desabilitar a criptografia TLS v1.

Primeiro, obtenha a ID do recurso da instância do Serviço de Domínio do Azure que habilita o LDAP. O exemplo a seguir obtém a ID de uma instância do Serviço de Domínio do Azure chamada `aaddscontoso.com` em um grupo de recursos chamado `aadds-rg`.

```azurecli
AADDS_ID=$(az resource show --resource-group aadds-rg --resource-type "Microsoft.AAD/DomainServices" --name aaddscontoso.com --query "id" --output tsv)
```

Execute o seguinte comando para desabilitar a TLS v1:

```azurecli
az resource update --ids $AADDS_ID --set properties.domainSecuritySettings.tlsV1=Disabled
```

A saída exibirá `"tlsV1": "Disabled"` para `domainSecuritySettings`, conforme mostrado no seguinte exemplo:

```text
"domainSecuritySettings": {
      "ntlmV1": "Enabled",
      "syncKerberosPasswords": "Enabled",
      "syncNtlmPasswords": "Enabled",
      "syncOnPremPasswords": "Enabled",
      "tlsV1": "Disabled"
}
```

Para obter mais informações, confira [Desabilitar criptografias fracas e sincronização de hash de senha para proteger um domínio gerenciado do Azure Active Directory Domain Services](/azure/active-directory-domain-services/secure-your-domain).

## <a name="wls-configuration"></a>Configuração do WLS

Esta seção ajuda você a coletar os valores de parâmetro do Azure AD DS implantados anteriormente.

Ao implantar um dos Aplicativos Azure listados em [Aplicativos Azure do Oracle WebLogic Server](/azure/virtual-machines/workloads/oracle/oracle-weblogic), você poderá optar por fazer com que a implantação se conecte automaticamente a um servidor LDAP pré-existente.  Como alternativa, você pode configurar a conexão do LDAP mais tarde invocando o submodelo de integração do Active Directory.  Essa abordagem é descrita no Apêndice A da [documentação oficial](https://wls-eng.github.io/arm-oraclelinux-wls/).  De qualquer forma, você deve ter os valores de parâmetro necessários para passar para o modelo do ARM.

| Nome do parâmetro | Descrição   | Detalhes | 
|----------------|---------------|---------|
| `aadsServerHost` | Host do servidor | Esse valor é o nome DNS público que você salvou ao concluir [Criar e configurar um domínio gerenciado do Azure Active Directory Domain Services](/azure/active-directory-domain-services/tutorial-create-instance). |
| `aadsPublicIP` | Endereço IP externo do LDAP Seguro | Esse valor é o **Endereço IP externo do LDAP Seguro** salvo na seção [Configurar zona DNS para acesso externo](/azure/active-directory-domain-services/tutorial-configure-ldaps#configure-dns-zone-for-external-access).|
| `wlsLDAPPrincipal` | Principal   | Volte para *LDP.exe*.  Execute as etapas a seguir para obter um valor adicional para `wlsLDAPPrincipal`. <ol><li>No menu **Exibir**, selecione **Árvore**.</li><li>Na caixa de diálogo **Modo de Exibição de Árvore**, deixe **BaseDN** em branco e selecione **OK**.</li><li>Clique com o botão direito do mouse no painel do lado direito e selecione **Limpar saída**.</li><li>Expanda o modo de exibição de árvore à esquerda e selecione a entrada que começa com "OU=AADDC Users".</li><li>No menu **Procurar**, selecione **Pesquisar**.</li><li>Na caixa de diálogo que aparece, aceite os padrões e selecione **Executar**.</li><li>Depois que a saída for exibida no painel do lado direito, selecione **Fechar**, ao lado de **Executar**.</li><li>Verifique a saída para a entrada **Dn** correspondente ao usuário que você adicionou ao grupo "Administradores de DC do AAD".  Ela começará com **Dn: CN=&lt;nome de usuário&gt;OU=AADDC Users"** .</li></ol> |
| `wlsLDAPGroupBaseDN` e `wlsLDAPUserBaseDN` | DN de base de usuário e DN de base de grupo | Para este tutorial, os valores para ambas as propriedades são os mesmos: a parte do **wlsLDAPPrincipal** após a primeira vírgula.|
| `wlsLDAPPrincipalPassword` | Senha para a entidade de segurança | Esse valor é a senha para o usuário que foi adicionado ao grupo de **Administradores de DC do AAD**. |
| `wlsLDAPProviderName` | Nome do Provedor | Esse valor pode ser deixado como padrão.  Ele é usado como o nome do provedor de autenticação no WLS. |
| `wlsLDAPSSLCertificate` | Confiar no repositório de chaves para a configuração de SSL | Esse foi o arquivo de valor *.cer* que você foi convidado a salvar ao concluir a etapa [Exportar um certificado para os computadores cliente](/azure/active-directory-domain-services/tutorial-configure-ldaps#export-a-certificate-for-client-computers).

### <a name="integrating-azure-ad-ds-ldap-with-wls"></a>como integrar o LDAP do Azure AD DS com o WLS

Com os valores de configuração acima em mãos e o LDAP do Azure AD DS implantado, agora é possível iniciar a configuração.  Há duas abordagens para concluir esse processo.

#### <a name="during-wls-deployment"></a>Durante a implantação do WLS

Acesse [Aplicativos Azure do Oracle WebLogic Server](/azure/virtual-machines/workloads/oracle/oracle-weblogic) e selecione o administrador ou uma das ofertas de cluster.  Ao implantar a oferta, uma das guias no processo de implantação será a do **Azure Active Directory**.  Alterne **Conectar ao Azure Active Directory** para **Sim**.  Preencha os valores com base nas informações coletadas na seção anterior.  Para o certificado, você deve carregar o arquivo `.cer` diretamente.

#### <a name="after-wls-deployment"></a>Após a implantação do WLS

Se você não tiver alternado **Conectar ao Azure Active Directory** para **Sim** no momento da implantação, poderá usar os valores coletados na seção anterior para fazer a configuração mais tarde.  Mais detalhes podem ser conferidos [na documentação oficial](https://wls-eng.github.io/arm-oraclelinux-wls/).

### <a name="validate-the-deployment"></a>Validar a implantação

Depois de implantar o WLS e configurar o LDAP usando um dos dois métodos acima, siga estas etapas para verificar se a integração foi bem-sucedida.

1. Visite o console de administração do WLS.
1. No navegador esquerdo, expanda a árvore para selecionar **Realms de Segurança** -> **myrealm** -> **Provedores**.
1. Se a integração tiver sido bem-sucedida, você encontrará o provedor do Azure AD, por exemplo `AzureActiveDirectoryProvider`.
1. No navegador esquerdo, expanda a árvore para selecionar **Realms de Segurança** -> **myrealm** -> **Usuários e Grupos**.
1. Se a integração tiver sido bem-sucedida, você encontrará usuários do provedor do Azure AD.

### <a name="lock-down-and-secure-ldap-access-over-the-internet"></a>Bloquear e proteger o acesso LDAP seguro na Internet

Ao criar o LDAP seguro nas etapas anteriores, definimos a origem como **Qualquer** para a regra `AllowLDAPS` no grupo de segurança de rede.  Agora que o servidor de administração do WLS foi implantado e conectado ao LDAP, obtenha seu endereço IP público usando o portal do Azure.  Reveja [Bloquear acesso LDAP seguro pela Internet](/azure/active-directory-domain-services/tutorial-configure-ldaps?branch=pr-en-us-778#lock-down-secure-ldap-access-over-the-internet) e altere **Qualquer** para o endereço IP específico do servidor de administração do WLS.

## <a name="clean-up-resources"></a>Limpar os recursos

Agora é hora de seguir as etapas na seção [Limpar recursos](/azure/active-directory-domain-services/tutorial-configure-ldaps#clean-up-resources) em [Configurar o LDAP seguro para um domínio gerenciado do Azure Active Directory Domain Services](/azure/active-directory-domain-services/tutorial-configure-ldaps#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Explore outros aspectos da migração de aplicativos do WebLogic Server para o Azure.

> [!div class="nextstepaction"]
> [Migrar aplicativos do WebLogic Server para as Máquinas Virtuais do Azure](./migrate-weblogic-to-virtual-machines.md)