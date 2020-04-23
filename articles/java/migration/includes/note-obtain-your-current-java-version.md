---
author: yevster
ms.author: yebronsh
ms.date: 1/22/2020
ms.openlocfilehash: 2fc4e3b43a051103e7aea6aa77f66666ca454910
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81672132"
---
<!-- Included in "### Switch to a supported platform" sections that have different (required) intro paragraphs. For example:

### Switch to a supported platform

App Service offers specific versions of Java SE. To ensure compatibility, migrate your application to one of the supported versions of in its current environment before you proceed with any of the remaining steps. Be sure to fully test the resulting configuration. Use the latest stable release of your Linux distribution in such tests.

-->

> [!NOTE]
> Essa validação é especialmente importante se o servidor atual estiver sendo executado em um JDK não compatível (como Oracle JDK ou IBM OpenJ9).

Para determinar a sua versão atual do Java, entre no servidor de produção e execute o seguinte comando:

```bash
java -version
```

Para obter a versão atual usada pelo Serviço de Aplicativo do Azure, baixe [Zulu 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk) se você pretende usar o runtime 8 do Java ou [Zulu 11](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts&architecture=x86-64-bit&package=jdk) se você pretende usar o runtime 11 do Java.
