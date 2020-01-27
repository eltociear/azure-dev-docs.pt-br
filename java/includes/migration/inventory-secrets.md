---
author: yevster
ms.author: yebronsh
ms.topic: include
ms.date: 1/20/2020
ms.openlocfilehash: 4228d1db44ac16da1c05fe97d20a3491cc9c5f51
ms.sourcegitcommit: 3585b1b5148e0f8eb950037345bafe6a4f6be854
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288565"
---
### <a name="inventory-secrets"></a>Segredos de inventário

#### <a name="passwords-and-secure-strings"></a>Senhas e cadeias de caracteres seguras

Verifique todas as propriedades e os arquivos de configuração nos servidores de produção para quaisquer senhas e cadeias de caracteres secretas. É necessário verificar *server.xml* e *context.xml* em $CATALINA_BASE/conf. Você também pode encontrar arquivos de configuração que contenham senhas ou credenciais dentro de seu aplicativo. Eles podem incluir *META-INF/context.xml* e, para aplicativos Spring boot, arquivos *application.properties* ou *application.yml*.

#### <a name="certificates"></a>Certificados

Documente todos os certificados usados para pontos de extremidade SSL públicos. Você pode exibir todos os certificados nos servidores de produção executando o seguinte comando:

```bash
keytool -list -v -keystore <path to keystore>
```
