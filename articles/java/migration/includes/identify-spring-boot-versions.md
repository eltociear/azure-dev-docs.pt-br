---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 34701a7d3fd85fea412be859dc21823df2dde053
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990188"
---
#### <a name="identify-spring-boot-versions"></a>Identificar as versões do Spring Boot

Examine as dependências de cada aplicativo que está sendo migrado para determinar a versão do Spring Boot.

##### <a name="maven"></a>Maven

Em projetos Maven, a versão do Spring Boot normalmente se encontra no elemento `<parent>` do arquivo POM:

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```

##### <a name="gradle"></a>Gradle

Em projetos Gradle, a versão do Spring Boot normalmente se encontrará na seção `plugins`, como a versão do plug-in `org.springframework.boot`:

```gradle
plugins {
  id 'org.springframework.boot' version '2.2.6.RELEASE'
  id 'io.spring.dependency-management' version '1.0.9.RELEASE'
  id 'java'
}
```
