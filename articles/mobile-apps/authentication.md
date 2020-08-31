---
title: Adicionar autenticação aos seus aplicativos móveis com o Visual Studio App Center e serviços do Azure
description: Saiba mais sobre serviços, como o Visual Studio App Center, que ajudam a configurar a autenticação do usuário e a permitir que os aplicativos móveis se autentiquem com contas sociais, Azure Active Directory e autenticação personalizada.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 2afdf7c4194052aff1fe7c7f915540b270ce588e
ms.sourcegitcommit: c012db9b6e369813c56f35a0ddbb1d3a73db5c4d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88889230"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Adicionar autenticação e gerenciar identidades de usuário em seus aplicativos móveis

Ter visibilidade do usuário e do comportamento dele no aplicativo permite que os desenvolvedores criem experiências personalizadas para envolver melhor os usuários. Os profissionais que criam aplicativos de colaboração para usuários dentro da sua organização ou uma nova plataforma de rede social precisam de uma maneira de autenticar usuários e gerenciar a identidades deles. Um serviço de gerenciamento de identidade é um dos recursos mais importantes de um serviço de back-end móvel.

Use os seguintes serviços para habilitar a autenticação de usuário nos seus aplicativos móveis.

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gerenciamento de identidade de empresa a consumidor (B2C) que os desenvolvedores podem usar para autenticar os clientes. Esse serviço de marca branca permite que os desenvolvedores personalizem e controlem como os usuários interagem com seus aplicativos Web, de área de trabalho, móveis ou de página única, com segurança. Usando o Azure AD B2C, os usuários podem se inscrever, entrar, redefinir senhas e editar perfis. O Azure AD B2C implementa um formulário dos protocolos OpenID Connect e OAuth 2.0. 

### <a name="azure-active-directory-b2c-features"></a>Recursos do Azure Active Directory B2C

- Autenticar os clientes com seu provedor de identidade preferencial de maneira segura.
- Gerenciar a identidade e o acesso do cliente.
- Obter suporte de entrada para redes sociais, como Facebook, GitHub, Google, LinkedIn, Twitter, WeChat e Weibo.
- Conectar-se às suas contas de usuário com protocolos padrão do setor, como OpenID Connect ou SAML, para possibilitar o gerenciamento de identidade em várias plataformas.
- Fornecer experiências de registro e de entrada com marca.
- Integrar-se facilmente a bancos de dados de CRM, ferramentas de análise de marketing e sistemas de verificação de contas.
- Capturar dados de entrada, preferência e conversão para clientes.

### <a name="azure-active-directory-b2c-references"></a>Referências do Azure Active Directory B2C

- [Azure portal](https://portal.azure.com/)
- [Documentação do Azure AD B2C](/azure/active-directory-b2c/)
- [Inícios Rápidos](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Amostras](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory

O [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e de acesso baseado em nuvem da Microsoft, que ajuda seus funcionários a entrarem e obter acesso a:

- Recursos externos, como o Microsoft 365, o portal do Azure e milhares de outros aplicativos SaaS (software como serviço).
- Recursos internos, como aplicativos em sua rede corporativa e intranet, juntamente com outros aplicativos de nuvem desenvolvidos por sua organização.

### <a name="azure-active-directory-features"></a>Recursos do Azure Active Directory

- Acesso contínuo e altamente seguro ao conectar os usuários aos aplicativos que eles precisam.
- Proteção de identidade abrangente e segurança aprimorada para identidades e acesso, com base no usuário, no local, no dispositivo, nos dados e no contexto do aplicativo.
- Milhares de aplicativos previamente integrados para aplicativos comerciais e personalizados, como o Microsoft 365, o Salesforce.com e o Box.
- Capacidade de gerenciar o acesso em escala.

### <a name="azure-active-directory-references"></a>Referências do Azure Active Directory

- [Azure portal](https://portal.azure.com/)
- [O que é o AD do Azure?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Introdução ao Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Inícios Rápidos](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)