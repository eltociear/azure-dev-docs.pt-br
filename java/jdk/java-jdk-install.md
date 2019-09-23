---
title: Instalar o JDK do Azul Zulu para o Azure e o Azure Stack
description: Como instalar os JDKs (Kits de Desenvolvimento Java) do Azul Zulu para desenvolvimento no Azure com Windows, Linux e Mac
author: bmitchell287
manager: douge
ms.author: brendm
ms.date: 04/19/2019
ms.devlang: java
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: 7d3d2af845ba2d8d1ba16b6dcd34b21c0853633f
ms.sourcegitcommit: 4a95777874ae3a3c760365148de868f937fdfd2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094874"
---
# <a name="install-the-jdk-for-azure-and-azure-stack"></a>Instalar o JDK para o Azure e o Azure Stack

Os builds do JDK do Azul Zulu para Azure – Edição Enterprise são uma distribuição sem custo, multiplataforma e pronta para produção do OpenJDK para Azure e Azure Stack da Microsoft e da Azul Systems. Eles contêm todos os componentes para criar e executar aplicativos Java SE.

Há [suporte para vários tipos de pacote de download para cada sistema operacional cliente](https://www.azul.com/downloads/azure-only/zulu/). Você também pode obter uma imagem de máquina virtual da Galeria do Azure Marketplace para as seguintes plataformas:

  * [Azul Zulu: Java 8 no Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804)
  * [Azul Zulu: Java 8 no Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-windows-2019)
  
  * [Azul Zulu: Java 11 no Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu11-ubuntu-1804)
  * [Azul Zulu: Java 11 no Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu11-windows-2019)


> [!NOTE]
> Estas instruções destinam-se à versão Java 8 de 64 bits do JDK. A Azul também fornece o JRE (Java Runtime Environment) como uma instalação autônoma. O JRE está incluído com a instalação do JDK.
>
>  Os pacotes Java 11 também são fornecidos na [página de downloads do Azure do Azul](https://www.azul.com/downloads/azure-only/zulu/).

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-windows"></a>Baixe e instale os builds do JDK do Azul Zulu para Azure – Edição Enterprise para Windows 

1. [Baixe o JDK 8 de 64 bits Azul Zulu como um MSI](https://repos.azul.com/azure-only/zulu/packages/zulu-11/11.0.3/zulu-11-azure-jdk_11.31.11-11.0.3-win_x64.msi) para um local no seu cliente, como `C:\Users\<your_login>\Downloads`. (Os pacotes .ZIP também são fornecidos na [página de downloads do Azure do Azul](https://www.azul.com/downloads/azure-only/zulu/).)

2. Navegue até o diretório e clique duas vezes no arquivo MSI baixado para iniciar a instalação.

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-mac"></a>Baixe e instale os builds do JDK do Azul Zulu para Azure – Edição Enterprise para Mac 

Estas etapas baixam um arquivo ZIP para o Mac. Também há uma versão DMG disponível.

1. [Baixe o JDK 8 de 64 bits Azul Zulu como um arquivo ZIP](https://repos.azul.com/azure-only/zulu/packages/zulu-11/11.0.3/zulu-11-azure-jdk_11.31.11-11.0.3-macosx_x64.zip) para um local no seu cliente, como `/Library/Java/JavaVirtualMachines/`. (Os pacotes .DMG também são fornecidos na [página de downloads do Azure do Azul](https://www.azul.com/downloads/azure-only/zulu/).)

2. Inicie o Localizador, navegue até o diretório de downloads e clique duas vezes no arquivo ZIP. Como alternativa, inicie uma janela de comando do terminal, navegue até o diretório e execute:

```cli
unzip <name_of_zulu_package>.zip
```

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-alpine-linux"></a>Baixe e instale os builds do JDK do Azul Zulu para Azure – Edição Enterprise para Linux Alpine

1. [Baixe o JDK 8 de 64 bits Azul Zulu como um arquivo TAR](https://repos.azul.com/azure-only/zulu/packages/zulu-11/11.0.3/zulu-11-azure-jdk_11.31.11-11.0.3-linux_x64.tar.gz) para um local no seu cliente, como `/usr/lib/jvm`. (Os pacotes .RPM e .DEB também são fornecidos na [página de downloads do Azure do Azul](https://www.azul.com/downloads/azure-only/zulu/).)

2. Vá para seu diretório e execute o seguinte comando para descompactar e expandir o arquivo:

    ```cli
    tar -xvf <name_of_zulu_package>.tar
    ```

## <a name="confirm-your-installation"></a>Confirmar sua instalação

Para confirmar sua instalação, vá para a linha de comando e execute `java -version`.

A saída do comando deverá ser:

```cli
$ java -version

openjdk version "1.8.0_212"
OpenJDK Runtime Environment (Zulu 8.38.0.13-macosx)-Microsoft-Azure-restricted (build 1.8.0_212-b04)
OpenJDK 64-Bit Server VM (Zulu 8.38.0.13-macosx)-Microsoft-Azure-restricted (build 25.212-b04, mixed mode)

```

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdks-from-a-yum-repository"></a>Baixe e instale os JDKs do Azul Zulu para Azure – Edição Enterprise de um repositório do Yum

Os JDKs do Azul Zulu são fornecidos em um [repositório Yum](https://repos.azul.com/azure-only/zulu-azure.repo) pela Azul.

**Para instalar o JDK do Azul Zulu para Java 8, execute os seguintes comandos da sua CLI:**

```cli
sudo rpm --import http://repos.azul.com/azul-repo.key
sudo curl http://repos.azul.com/azure-only/zulu-azure.repo -o /etc/yum.repos.d/zulu-azure.repo
sudo yum -q -y update
sudo yum -q -y install zulu-8-azure-jdk
```

Para Java 11, execute:

```cli
sudo rpm --import http://repos.azul.com/azul-repo.key
sudo curl http://repos.azul.com/azure-only/zulu-azure.repo -o /etc/yum.repos.d/zulu-azure.repo
sudo yum -q -y update
sudo yum -q -y install zulu-11-azure-jdk
```

Para Java 12 (Versão Prévia), execute:

```cli
sudo rpm --import http://repos.azul.com/azul-repo.key
sudo curl http://repos.azul.com/azure-only/zulu-azure.repo -o /etc/yum.repos.d/zulu-azure.repo
sudo yum -q -y update
sudo yum -q -y install zulu-12-azure-jdk
```

**Para atualizar um pacote do Zulu JDK 8 de um repositório Yum:**

```cli
sudo yum -q -y install zulu-8-azure-jdk
```

(Altere o número de versão no comando acima se você estiver usando a versão 11 ou 12.)

**Para remover um pacote do Zulu JDK 8 de um repositório Yum:**

```cli
sudo yum -y erase zulu-8-azure-jdk
```
(Altere o número de versão no comando acima se você estiver usando a versão 11 ou 12.)

## <a name="download-and-install-the-azul-zulu-jdks-from-an-apt-get-repository"></a>Baixar e instalar os JDKs do Azul Zulu de um repositório apt-get

Os JDKs do Azul Zulu também são fornecidos em um [repositório apt-get](https://repos.azul.com/azure-only/zulu/apt) pela Azul.

**Para instalar o JDK do Azul Zulu para Java 8 com apt-get, execute os seguintes comandos da sua CLI:**

```cli
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
sudo apt-get -q update
sudo apt-get -y install zulu-8-azure-jdk
```

Para Java 11, execute:

```cli
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
sudo apt-get -q update
sudo apt-get -y install zulu-11-azure-jdk
```

Para Java 12 (Versão Prévia), execute:

```cli
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
sudo apt-get -q update
sudo apt-get -y install zulu-12-azure-jdk
```

**Para atualizar um pacote do Zulu JDK 8 de um repositório apt-get:**

```cli
sudo apt-get -q update
sudo apt-get -y install zulu-8-azure-jdk
```

A versão anterior será removida automaticamente.
(Altere o número de versão no comando acima se você estiver usando a versão 11 ou 12.)

**Para remover um pacote do Zulu JDK 8 de um repositório apt-get:**

```cli
sudo apt-get -y purge zulu-8-azure-jdk
```

(Altere o número de versão no comando acima se você estiver usando a versão 11 ou 12.)

Para obter mais diretrizes sobre como preparar, instalar e gerenciar seus JDKs do Azul Zulu para o desenvolvimento do Azure, leia os [documentos oficiais do Zulu](https://docs.azul.com/zulu/zuludocs/index.htm).

