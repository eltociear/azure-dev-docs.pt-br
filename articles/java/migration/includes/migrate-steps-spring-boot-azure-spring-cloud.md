---
author: yevster
ms.author: yebronsh
ms.date: 8/25/2020
ms.openlocfilehash: 7970cae2b9ac39f7012e74b1334d12b2c5006af0
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062036"
---
### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Criar uma instância e aplicativos do Azure Spring Cloud

Provisione uma instância do Azure Spring Cloud na sua assinatura do Azure, se não houver uma. Em seguida, crie um aplicativo nela. Para saber mais, confira [Início Rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](configure-persistent-storage-azure-spring-cloud.md)]

[!INCLUDE [migrate-all-certificates-to-keyvault-azure-spring-cloud](migrate-all-certificates-to-keyvault-azure-spring-cloud.md)]

### <a name="remove-application-performance-management-apm-integrations"></a>Remover as integrações de APM (gerenciamento de desempenho de aplicativos)

Elimine as integrações com ferramentas/agentes de APM. Para obter informações sobre como configurar o gerenciamento de desempenho com o Azure Monitor, confira a seção [Pós-migração](#post-migration).

### <a name="disable-metrics-clients-and-endpoints-in-your-applications"></a>Desabilitar os clientes e os pontos de extremidade de métricas nos aplicativos

Remova os clientes de métricas usados ou os pontos de extremidade de métricas expostos nos aplicativos.

### <a name="deploy-the-application"></a>Implantar o aplicativo

Implante cada um dos microsserviços migrados (não incluindo os servidores de Configuração e Registro do Spring Cloud), conforme descrito no [Guia de Início Rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

### <a name="configure-per-service-secrets-and-externalized-settings"></a>Configurar os segredos por serviço e definir as configurações externas

Você pode injetar as definições de configuração por serviço em cada serviço como variáveis de ambiente. Use as seguintes etapas no portal do Azure:

1. Navegue até a instância do Azure Spring Cloud e selecione **Aplicativos**.
1. Escolha o serviço a ser configurado.
1. Selecione **Configuração**.
1. Insira as variáveis para a configuração.
1. Clique em **Salvar**.

![Definições de Configuração de Aplicativos do Spring Cloud](../media/migrate-spring-cloud-to-azure-spring-cloud/spring-cloud-app-configuration-settings.png)

### <a name="migrate-and-enable-the-identity-provider"></a>Migrar e habilitar o provedor de identidade

Se qualquer um dos aplicativos Spring Cloud requerer autenticação ou autorização, verifique se eles estão configurados para acessar o provedor de identidade:

* Se o provedor de identidade for o Azure Active Directory, nenhuma alteração será necessária.
* Se o provedor de identidade for uma floresta local do Active Directory, considere implementar uma solução de identidade híbrida com o Azure Active Directory. Para obter mais informações, confira a [Documentação sobre identidade híbrida](/azure/active-directory/hybrid/).
* Se o provedor de identidade for outra solução local, como o PingFederate, confira o tópico [Instalação personalizada do Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom) para configurar a federação com o Azure Active Directory. Como alternativa, considere o Spring Security para o uso do provedor de identidade por meio do [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) ou do [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2).

### <a name="expose-the-application"></a>Expor o aplicativo

Por padrão, os aplicativos implantados no Azure Spring Cloud não são visíveis externamente. Você pode expor seu aplicativo tornando-o público com o seguinte comando:

```azurecli
az spring-cloud app update -n <application name> --is-public true
```

Ignore esta etapa se estiver usando um Spring Cloud Gateway ou pretender usá-lo (saiba mais sobre isso na seção a seguir).
