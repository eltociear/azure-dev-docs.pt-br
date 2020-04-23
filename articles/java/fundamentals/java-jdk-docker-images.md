---
title: Usar imagens do Docker com um JDK para desenvolvimento em Java do Azure
description: Saiba como usar imagens do Docker com um JDK (Java Development Kit) para Azure usando a interface de linha de comando.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: b3a046563fa51efae32fd45e6154901539a8f4c8
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81670732"
---
# <a name="use-docker-with-a-java-development-kit-jdk-for-azure"></a>Usar o Docker com um JDK (Java Development Kit) para Azure

Este artigo descreve como usar o Docker com um JDK (Java Development Kit) para Azure. Imagens do Docker criadas previamente para Java 7, 8 e 11 estão disponíveis por meio do [Docker Hub](https://hub.docker.com/_/microsoft-java-se).

Imagens de contêiner do Docker certificadas para o JDK do Zulu, o JRE e o JRE sem periféricos em várias imagens de SO base estão disponíveis no Docker Hub:

* [JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [JRE](https://hub.docker.com/_/microsoft-java-jre)
* [JRE sem periféricos](https://hub.docker.com/_/microsoft-java-jre-headless)

## <a name="running-a-docker-image"></a>Como executar uma imagem do Docker

Imagens do Docker podem ser executadas usando a sintaxe `$ docker run mcr.microsoft.com/java/jdk:tag java`, conforme mostrado no exemplo a seguir.

```cli
docker run mcr.microsoft.com/java/jdk:8-zulu-alpine java -version
```

## <a name="creating-a-docker-image"></a>Como criar uma imagem do Docker

Você pode criar uma imagem usando imagens do Docker Hub oficiais da Microsoft, conforme mostrado nos exemplos a seguir.

### <a name="create-a-docker-file"></a>Crie uma imagem do Docker

```cli
FROM mcr.microsoft.com/java/jdk:8-zulu-alpine
  
RUN echo $' \
  
public class HelloWorld { \
   public static void main(String[] args) { \
      // Prints "Hello, World" in the terminal window. \
      System.out.println("Hello, World - From Microsoft Azure !!!"); \
   } \
}' > HelloWorld.java
  
RUN javac HelloWorld.java
  
CMD ["java", "HelloWorld"]
```

### <a name="build-a-docker-image"></a>Compilar uma imagem do docker

```cli
docker build -t hello-world
```

### <a name="run-the-new-image"></a>Executar a nova imagem

```cli
docker run hello-world
```

Você verá esta saída:

```output
Hello World - From Microsoft Azure !!!
```
