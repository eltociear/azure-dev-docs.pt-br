---
author: yevster
ms.author: yebronsh
ms.date: 5/19/2020
ms.openlocfilehash: 51ed106fde71e37467571baab2b327b092dddc15
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507618"
---
### <a name="migrate-and-enable-the-identity-provider"></a>Migrar e habilitar o provedor de identidade

Se o aplicativo exigir autenticação ou autorização, verifique se ele está configurado para acessar o provedor de identidade usando as seguintes diretrizes:

* Se o provedor de identidade for o Azure Active Directory, nenhuma alteração será necessária.
* Se o provedor de identidade for uma floresta local do Active Directory, considere implementar uma solução de identidade híbrida com o Azure Active Directory. Para obter mais informações, confira a [Documentação sobre identidade híbrida](/azure/active-directory/hybrid/).
* Se o provedor de identidade for outra solução local, como o PingFederate, confira o tópico [Instalação personalizada do Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom) para configurar a federação com o Azure Active Directory. Como alternativa, considere o Spring Security para o uso do provedor de identidade por meio do [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) ou do [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2).
