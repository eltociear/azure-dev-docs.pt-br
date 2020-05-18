---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 6fdf908458eec428f4e01d1b5f20fcbf4e039dbe
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990218"
---
### <a name="configure-persistent-storage"></a>Configurar um armazenamento persistente

Se qualquer parte do aplicativo ler ou gravar no sistema de arquivos local, você precisará configurar o armazenamento persistente para substituir o sistema de arquivos local. Para obter mais informações, confira [Usar o armazenamento persistente no Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-persistent-storage).

Você deveria gravar arquivos temporários no diretório `/tmp`. Para a independência do SO, você pode obter esse diretório usando `System.getProperty("java.io.tmpdir")`. Você também pode usar [`java.nio.Files::createTempFile`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/file/Files.html#createTempFile(java.lang.String,java.lang.String,java.nio.file.attribute.FileAttribute...)) para criar arquivos temporários.
