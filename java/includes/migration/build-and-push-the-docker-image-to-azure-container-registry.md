---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 8af791a3b85f5e64865cee965a37797327db0b0d
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897599"
---
### <a name="build-and-push-the-docker-image-to-azure-container-registry"></a>Compilar e efetuar push da imagem do Docker para o Registro de Contêiner do Azure

Depois de criar o Dockerfile, você precisará compilar a imagem do Docker e publicá-la no seu registro de contêiner do Azure.

Se você usou nosso [Início Rápido do Contêiner do WildFly no repositório do GitHub](https://github.com/Azure/wildfly-container-quickstart), o processo de compilação e push de sua imagem para o registro de contêiner do Azure seria equivalente a invocar os três comandos a seguir.

Nesses exemplos, a variável de ambiente `MY_ACR` contém o nome do registro de contêiner do Azure e a variável `MY_APP_NAME` contém o nome do aplicativo Web que você deseja usar em seu registro de contêiner do Azure.

Compilar o arquivo WAR:

```shell
mvn package
```

Fazer logon no em seu registro de contêiner do Azure:

```shell
az acr login -n ${MY_ACR}
```

Compilar e efetuar push da imagem:

```shell
az acr build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME} -f src/main/docker/Dockerfile .
```

Como alternativa, você pode usar a CLI do Docker para compilar e testar a imagem localmente, conforme mostrado nos comandos a seguir. Essa abordagem pode simplificar o teste e refinar a imagem antes da implantação inicial no ACR. No entanto, ela requer que você instale a CLI do Docker e verifique se o daemon do Docker está em execução.

Compilar a imagem:

```shell
docker build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Executar a imagem localmente:

```shell
docker run -it -p 8080:8080 ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Agora, você pode configurar seu aplicativo em [http://localhost:8080](http://localhost:8080).

Fazer logon no em seu registro de contêiner do Azure:

```shell
az acr login -n ${MY_ACR}
```

Efetuar push da imagem ao registro de contêiner do Azure:

```shell
docker push ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Para obter informações mais detalhadas sobre como compilar e armazenar imagens de contêiner no Azure, confira o módulo do Learn [Compilar e armazenar imagens de contêiner com o Registro de Contêiner do Azure](https://docs.microsoft.com/learn/modules/build-and-store-container-images/).