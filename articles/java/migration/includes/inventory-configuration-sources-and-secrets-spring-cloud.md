---
author: yevster
ms.author: yebronsh
ms.date: 5/4/2020
ms.openlocfilehash: b15ebf1491dd494701dd5c18e0248e73bdd86f2c
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990198"
---
### <a name="inventory-configuration-sources-and-secrets"></a>Inventariar as fontes e os segredos de configuração

#### <a name="inventory-passwords-and-secure-strings"></a>Inventariar as senhas e proteger as cadeia de caracteres

Verifique todas as propriedades e os arquivos de configuração, assim como as variáveis de ambiente, nas implantações de produção em busca de senhas e cadeias de caracteres secretas. Em um aplicativo Spring Cloud, normalmente é possível encontrar essas cadeias de caracteres no arquivo *application.properties* ou no *application.yml* em serviços individuais ou no repositório do Spring Cloud Config.

[!INCLUDE [inventory-certificates-h4](inventory-certificates-h4.md)]

#### <a name="determine-whether-spring-cloud-vault-is-used"></a>Determinar se o Spring Cloud Vault é usado

Se você usar o Spring Cloud Vault para armazenar e acessar segredos, identifique o repositório de segredos de backup (por exemplo, HashiCorp Vault ou CredHub). Em seguida, identifique todos os segredos usados pelo código de aplicativo.

#### <a name="locate-the-configuration-server-source"></a>Localize a origem do servidor de configuração

Se o aplicativo usar um [Servidor do Spring Cloud Config](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server), identifique onde a configuração está armazenada. Normalmente será possível encontrar essa configuração no arquivo *bootstrap.yml* ou no *bootstrap.properties* ou, às vezes, no arquivo *application.yml* ou no *application.properties*. A configuração se parecerá com o seguinte exemplo:

```properties
spring.cloud.config.server.git.uri: file://${user.home}/spring-cloud-config-repo
```

Embora o Git seja usado com mais frequência como o armazenamento de dados de backup do Spring Cloud Config, como mostrado anteriormente, um dos outros back-ends possíveis pode estar em uso. Confira a [Documentação do Spring Cloud Config](https://cloud.spring.io/spring-cloud-config/reference/html/#_environment_repository) para obter informações sobre outros back-ends, como o [JDBC (banco de dados relacional)](https://cloud.spring.io/spring-cloud-config/reference/html/#_jdbc_backend), o [SVN](https://cloud.spring.io/spring-cloud-config/reference/html/#_version_control_backend_filesystem_use) e [o sistema de arquivos local](https://cloud.spring.io/spring-cloud-config/reference/html/#_file_system_backend).

> [!NOTE]
> Se os dados do servidor de configuração estiverem armazenados localmente, como o GitHub Enterprise, você precisará disponibilizá-los para o Azure Spring Cloud por meio de um repositório Git.
