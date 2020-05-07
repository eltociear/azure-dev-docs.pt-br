---
author: yevster
ms.author: yebronsh
ms.date: 1/20/2020
ms.openlocfilehash: e37d0337361ce742ce7f7994ab634e63bc4b2ead
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81671642"
---
### <a name="inventory-secrets"></a>Segredos de inventário

#### <a name="passwords-and-secure-strings"></a>Senhas e cadeias de caracteres seguras

Verifique todas as propriedades e os arquivos de configuração nos servidores de produção para quaisquer senhas e cadeias de caracteres secretas. É necessário verificar *server.xml* e *context.xml* em *$CATALINA_BASE/conf*. Você também pode encontrar arquivos de configuração que contenham senhas ou credenciais dentro de seu aplicativo. Eles podem incluir *META-INF/context.xml* e, para aplicativos Spring boot, arquivos *application.properties* ou *application.yml*.
