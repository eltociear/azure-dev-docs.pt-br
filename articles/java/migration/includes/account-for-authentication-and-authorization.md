---
author: edburns
ms.author: edburns
ms.date: 08/09/2020
ms.openlocfilehash: e006f2ec9d6f0d3b2d83a6653d65da19489dc221
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88052244"
---
### <a name="account-for-authentication-and-authorization"></a>Conta para autenticação e autorização

A maioria dos aplicativos tem algum tipo de autenticação e autorização.  Se você usar o LDAP para autenticação, o WebLogic Server no Azure dará suporte à integração automática. A oferta do marketplace usa o Azure AD DS (Azure Active Directory Domain Services) com LDAP seguro.  A oferta cria o realm padrão para o WebLogic Server com base em dados no Azure AD DS.  Para obter mais informações, confira [Autorização e autenticação do usuário final para migração de aplicativos Java no WebLogic Server para o Azure](../migrate-weblogic-with-aad-ldap.md).
