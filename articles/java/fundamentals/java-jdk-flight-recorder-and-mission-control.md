---
title: Examinar dados com o Zulu Flight Recorder e o Mission Control
description: Diretrizes para usar o Zulu Flight Recorder e o Mission Control para coletar e examinar os dados de aplicativo.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: afd95e7f39fb9abdfe2261c8ef0f5ac961347ffb
ms.sourcegitcommit: bbfa6e0dfb3c8e66e5f47b080590105787a6e74b
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85418194"
---
# <a name="monitor-and-manage-java-workloads-with-zulu-flight-recorder-and-zulu-mission-control"></a>Monitorar e gerenciar cargas de trabalho Java com o Zulu Flight Recorder e o Zulu Mission Control

Este artigo mostra como monitorar e gerenciar cargas de trabalho Java com o Zulu Flight Recorder e o Zulu Mission Control.

O Zulu Mission Control é um build totalmente testado do JDK Mission Control. Em 2018, a Oracle transformou o Mission Control em software livre. Ele é gerenciado como um projeto sob o guarda-chuva do OpenJDK. Junto com o Flight Recorder, o Mission Control oferece funcionalidades de gerenciamento e de monitoramento interativos de baixa sobrecarga para cargas de trabalho Java.

O Zulu Mission Control é compatível com os seguintes JDKs/JREs:

* Zulu 12.1 e posteriores
* Zulu 11.0 e posteriores
* Zulu 8u202 (8.36) e posteriores
* Oracle OpenJDK 11+15 e posteriores
* Oracle Java 11.0 e posteriores
* Oracle Java 8.0 e posteriores

Siga as etapas abaixo para instalar o Zulu Mission Control, conectar-se a uma JVM (Máquina Virtual Java) e obter visibilidade em tempo real de todos os aspectos de um aplicativo em execução.

1. [Instalar um JDK/JRE compatível com Zulu Mission Control](java-jdk-install.md).

2. Baixe o Zulu Mission Control do [site de download da Azul](https://www.azul.com/products/zulu-mission-control/), escolha a versão apropriada para seu sistema, salve-o localmente e altere para o respectivo diretório.

3. Expanda o arquivo baixado.

    **Linux:**

    ```cli
    tar -xzvf zmc7.0.0-EA-linux_x64.tar.gz
    ```

    **Windows:**

    ```cli
    unzip -zxvf zmc7.0.0-EA-win_x64.zip
    ```

    **macOS:**

    ```cli
    tar -xzvf zmc7.0.0-EA-macosx_x64.tar.gz
    ```

4. Inicie seu aplicativo Java usando um dos JDKs compatíveis. Por exemplo:

    ```cli
    $JAVA_HOME/bin/java -jar MyApplication.jar
    ```

5. Iniciar o Zulu Mission Control

    **Linux:**

    ```cli
    zmc7.0.0-EA-linux_x64/zmc
    ```

    **Windows:**

    ```cli
    zmc7.0.0-EA-win_x64\zmc.exe
    ```

    **macOS:**

    ```cli
    zmc7.0.0-EA-macosx_x64/Zulu\ Mission\ Control.app/Contents/MacOS/zmc
    ```

6. Alterne a instalação da JVM para o Mission Control (opcional).

    No Windows, *zmc.exe* usará a instalação da JVM padrão configurada no Registro. É necessário iniciar o Zulu Mission Control por meio de um JDK completo para que você possa detectar automaticamente instâncias locais da JVM. Se você usar um JRE, verá o seguinte aviso:

    > [!div class="mx-imgBorder"]
    ![Aviso se a instalação do JDK for somente para JRE](media/jfr-jre-warning-message.png)

    Para alterar a JVM usada pelo Mission Control, siga estas etapas:

    1. Abra o arquivo de configuração *zmc.ini*, localizado no mesmo diretório que *zmc.exe*.

    2. Antes da linha `-vmargs`, adicione duas linhas:

        * Na primeira linha, escreva `–vm`.
        * Na segunda linha, escreva o caminho para a instalação do JDK. (Por exemplo, `C:\Program Files\Java\jdk1.8.0_212\bin\javaw.exe`).

7. Localize a JVM que está executando seu aplicativo.

    1. No painel superior esquerdo da janela do Zulu Mission Control, selecione a guia rotulada **Navegador da JVM**.

    2. Selecione e expanda o item de lista no canto superior esquerdo da instância da JVM que está executando seu aplicativo.

    > [!div class="mx-imgBorder"]
    ![Expanda o item de lista no canto superior esquerdo de sua instância da JVM](media/jfr-jvm-instance-dashboard.png)

8. Inicie uma Gravação de Voo, se necessário.

    1. Se o Flight Recorder exibir "Nenhuma Gravação", inicie uma. Para fazer isso, clique com o botão direito do mouse na linha do Flight Recorder na guia do Navegador da JVM e depois selecione **Iniciar Gravação de Voo**.

    2. Selecione uma gravação de duração fixa ou uma gravação contínua e uma configuração de Criação de Perfil (refinada) ou uma configuração Contínua (sobrecarga mais baixa) e selecione **Concluir**.

    > [!div class="mx-imgBorder"]
    ![Iniciar uma Gravação de Voo](media/jfr-start-flight-recording.png)

9. Despeje a Gravação de Voo.

    1. Uma gravação de voo deve aparecer embaixo da linha do Flight Recorder no navegador da JVM. Clique com o botão direito do mouse na linha que representa a Gravação de Voo e selecione **Despejar toda a gravação**.

    2. Uma nova guia será exibida no painel grande à direita da janela do Zulu Mission Control. Esse painel representa a gravação de voo que acaba de ser despejada da JVM que está executando seu aplicativo.

10. Examinar a gravação de voo usando o Zulu Mission Control
    1. Se ainda não estiver ativada, selecione a guia rotulada **Estrutura de tópicos** no painel esquerdo da janela do Zulu Mission Control. Essa guia contém diferentes exibições dos dados coletados na Gravação de Voo.

    > [!div class="mx-imgBorder"]
    ![Examinar a Gravação de Versão Piloto](media/jfr-zulu-mission-control-data.png)

## <a name="resources"></a>Recursos

Também preparamos um [vídeo de demonstração](https://www.azul.com/presentation/azul-webinar-open-source-flight-recorder-and-mission-control-managing-and-measuring-openjdk-8-performance/) narrado por Simon Ritter, subdiretor de tecnologia da Azul Systems. O vídeo explica a configuração e a instalação do Flight Recorder Voo e do Zulu Mission Control. A discussão sobre o Flight Recorder começa em 31:30.
