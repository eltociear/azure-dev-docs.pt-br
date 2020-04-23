---
title: Motivos para mudar para o Java 11
titleSuffix: Azure
description: Um documento de nível de resumo destinado a tomadores de decisão que estão avaliando os benefícios de mudar do Java 8 para o Java 11.
author: dsgrieve
manager: maverbur
tags: java
ms.topic: article
ms.date: 11/19/2019
ms.author: dagrieve
ms.openlocfilehash: c0a2f46f8a3249f6c9580e823e102a86291e15e7
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81670632"
---
# <a name="reasons-to-move-to-java-11"></a>Motivos para mudar para o Java 11

A questão não é *se* você deve mudar para o Java 11, mas *quando*. Nos próximos anos, o Java 8 não terá mais suporte e os usuários terão de mudar para o Java 11. Defendemos os benefícios de mudar para o Java 11 e incentivamos as equipes a fazê-lo o mais breve possível.

Desde o Java 8, novos recursos foram adicionados e melhorias foram feitas. Existem adições e modificações visíveis na API, além de melhorias na inicialização, no desempenho e no uso de memória.

## <a name="transitioning-to-java-11"></a>Transição para o Java 11

A transição para o Java 11 pode ser feita de maneira gradual. *Não* é necessário que o código use módulos Java para executar em Java 11. O Java 11 pode ser usado para executar o código desenvolvido e criado com o JDK 8.
No entanto, existem alguns possíveis problemas, principalmente em relação à API preterida, aos carregadores de classe e à reflexão.

O Grupo de Engenharia Java da Microsoft tem um guia para [fazer a transição do Java 8 para o Java 11](./transition-from-java-8-to-java-11.md). O [Guia de Migração da Plataforma Java e do Oracle JDK 9 de Edição Standard](https://docs.oracle.com/javase/9/migrate/toc.htm) e [O Estado do Sistema de Módulo: Compatibilidade e Migração](http://openjdk.java.net/projects/jigsaw/spec/sotms/#compatibility--migration) são outros guias úteis. 

## <a name="high-level-changes-between-java-8-and-11"></a>Alterações de alto nível entre o Java 8 e 11

Esta seção não enumera todas as alterações feitas nas versões 9 \[[1](#ref1)\], 10 \[[2](#ref2)\] e 11 \[[3](#ref3)\] do Java. As alterações que afetam o desempenho, o diagnóstico e a produtividade serão destacadas.

### <a name="modules-4"></a>\[[4](#ref4)\] – Módulos

Os módulos abordam problemas de configuração e encapsulamento que são difíceis de gerenciar em aplicativos de grande escala em execução no *classpath*. Um *módulo* é uma coleção de descrição automática de classes e interfaces Java e recursos relacionados.

Os módulos possibilitam personalizar as configurações de runtime que contêm somente os componentes exigidos por um aplicativo. Essa personalização cria um volume menor e permite que um aplicativo seja vinculado de maneira estática, usando [jlink](https://docs.oracle.com/en/java/javase/11/tools/jlink.html) em um runtime personalizado para implantação. Esse volume menor pode ser particularmente útil em uma arquitetura de microsserviços.

Internamente, a JVM é capaz de aproveitar os módulos de maneira a tornar o carregamento de classes mais eficiente. O resultado é um runtime menor, mais leve e mais rápido para iniciar. As técnicas de otimização usadas pela JVM para melhorar o desempenho do aplicativo podem ser mais eficazes porque os módulos codificam os componentes necessários de uma classe.

Para os programadores, os módulos ajudam a impor um encapsulamento forte, exigindo uma declaração explícita de quais pacotes um módulo exporta e quais componentes são necessários, além de restringir o acesso reflexivo.
Esse nível de encapsulamento torna um aplicativo mais seguro e fácil de manter.

Um aplicativo pode continuar a usar o *classpath* e não precisa fazer a transição para os módulos como um requisito para execução no Java 11.

### <a name="profiling-and-diagnostics"></a>Criação de perfil e diagnóstico

#### <a name="java-flight-recorder-5"></a>\[[5](#ref5)\] – Java Flight Recorder

O JFR (Java Flight Recorder) coleta dados de diagnóstico e de criação de perfil de um aplicativo Java em execução. O JFR tem pouco impacto em um aplicativo Java em execução. Os dados coletados podem ser analisados com o JMC (Java Mission Control) e outras ferramentas. Embora o JFR e o JMC fossem recursos comerciais no Java 8, ambos são softwares livres no Java 11.

#### <a name="java-mission-control-6"></a>\[[6](#ref6)\] – Java Mission Control

O JMC (Java Mission Control) fornece uma exibição gráfica dos dados coletados pelo JFR (Java Flight Recorder), além de ser um software livre em Java
11. Além das informações gerais sobre o aplicativo em execução, o JMC permite que o usuário faça uma busca detalhada nos dados. O JFR e o JMC podem ser usados para diagnosticar problemas de runtime, como vazamentos de memória, sobrecarga de GC, métodos quentes, gargalos de thread e bloqueio da E/S.

#### <a name="unified-logging-7"></a>Log unificado \[[7](#ref7)\]

O Java 11 tem um sistema de registro em log comum para todos os componentes da JVM.
Esse sistema de log unificado permite que o usuário defina quais componentes registrar em log e em qual nível. Esse log refinado é útil para executar a análise da causa raiz em falhas da JVM e para diagnosticar problemas de desempenho em um ambiente de produção.

#### <a name="low-overhead-heap-profiling-8"></a>Criação de perfil de heap com baixa sobrecarga \[[8](#ref8)\]

Uma nova API foi adicionada à JVMTI (Interface de Ferramenta da Máquina Virtual Java) para amostragem de alocações do heap de Java. A amostragem tem baixa sobrecarga e pode ser habilitada continuamente. Embora a alocação do heap possa ser monitorada com o JFR (Java Flight Recorder), o método de amostragem no JFR funciona somente nas alocações. A implementação do JFR também pode perder alocações. Por outro lado, a amostragem do heap no Java 11 pode fornecer informações sobre objetos dinâmicos e inativos.

Os fornecedores de APM (Monitoramento do desempenho de aplicativos) estão começando a utilizar esse novo recurso e o Java Engineering Group está investigando o uso potencial dele com as ferramentas de monitoramento de desempenho do Azure.

#### <a name="stackwalker-9"></a>\[[9](#ref9)\] – StackWalker

Obter um instantâneo da pilha para o thread atual geralmente é usado durante o registro em log. O problema é quanto do rastreamento da pilha deve ser registrado e se deve ser registrado ou não. Por exemplo, talvez você queira ver o rastreamento de pilha somente para uma determinada exceção de um método. A classe StackWalker (adicionada no Java 9) fornece um instantâneo da pilha e de métodos que oferecem ao programador um controle refinado sobre como consumir o rastreamento de pilha.

### <a name="garbage-collection-10"></a>Coleta de lixo \[[10](#ref10)\]

Os seguintes coletores de lixo estão disponíveis no Java 11: Serial, Paralelo, Garbage First e Epsilon. O coletor de lixo padrão no Java 11 é o G1GC (Coletor de Lixo Garbage First).

Três outros coletores são mencionados aqui para fins de verificação. O ZGC (Coletor de Lixo Z) é um coletor simultâneo de baixa latência que tenta manter os tempos de pausa em 10 ms. O ZGC está disponível como um recurso experimental no Java 11. O Shenandoah é um coletor de baixa pausa que reduz os tempos de pausa do GC executando mais coletas de lixo simultaneamente com o programa Java em execução.
O Shenandoah é um recurso experimental no Java 12, mas existem backports para o Java 11. O coletor CMS (Concurrent Mark and Sweep) está disponível, mas foi preterido desde o Java 9.

A JVM define padrões de GC para o caso de uso médio. Esses padrões, além de outras configurações de GC, geralmente precisam ser ajustados para uma taxa de transferência ou latência ideal, de acordo com os requisitos do aplicativo.
O ajuste adequado exige um profundo conhecimento de GC, experiência que o [Java Engineering Group da Microsoft](mailto:javaplatformgroup@microsoft.com) fornece.

#### <a name="g1gc"></a>G1GC

O coletor de lixo padrão no Java 11 é o G1GC (coletor de lixo G1). O objetivo do G1GC é manter um equilíbrio entre a latência e a taxa de transferência. O coletor de lixo G1 tenta obter alta taxa de transferência cumprindo as metas de tempo de pausa com alta probabilidade. O G1GC foi projetado para evitar coleções completas, mas quando as coleções simultâneas não podem recuperar a memória com rapidez suficiente, ocorrerá um GC de fallback completo. O GC completo usa o mesmo número de threads de trabalho paralelos de coleções pequenas e mistas.

#### <a name="parallel-gc"></a>GC paralelo

O coletor paralelo é o coletor padrão no Java 8. O GC paralelo é um coletor de taxa de transferência que usa vários threads para acelerar a coleta de lixo.

#### <a name="epsilon-11"></a>\[[11](#ref11)\] – Epsilon

O coletor de lixo Epsilon controla as alocações, mas não recupera nenhuma memória. Quando o heap for esgotado, a JVM será desligada.
O Epsilon é útil para serviços de curta duração e para aplicativos conhecidos por não terem lixo.

#### <a name="improvements-for-docker-containers-12"></a>Melhorias dos contêineres do Docker \[[12](#ref12)\]

Antes do Java 10, as restrições de memória e CPU definidas em um contêiner não eram reconhecidas pela JVM. No Java 8, por exemplo, a JVM usará como padrão o tamanho máximo do heap para ¼ da memória física do host subjacente. Ao começar com o Java 10, a JVM usa restrições definidas por cgroups (grupos de controle de contêiner) para definir os limites de memória e da CPU (consulte a observação abaixo).
Por exemplo, o tamanho máximo do heap padrão é ¼ do limite de memória do contêiner (como 500 MB para -m2G).

As opções de JVM também foram adicionadas para fornecer aos usuários do contêiner do Docker um controle refinado sobre a quantidade de memória do sistema que será usada para o heap do Java.

Esse suporte é habilitado por padrão e está disponível somente em plataformas baseadas em Linux.

> [!NOTE]
> A maior parte do trabalho de habilitação do cgroup foi reportada para o Java 8 com base no jdk8u191. Outras melhorias podem não ser necessariamente reportadas para o 8.

#### <a name="multi-release-jar-files-13"></a>Arquivos JAR de várias versões \[[13](#ref13)\]

É possível criar um arquivo jar no Java 11 que contenha várias versões específicas de Java de arquivos de classe. Os arquivos jar de várias versões possibilitam aos desenvolvedores de bibliotecas dar suporte a várias versões do Java sem precisar enviá-las de arquivos jar. Para o consumidor dessas bibliotecas, os arquivos jar de várias versões resolvem o problema de precisar fazer a correspondência de arquivos jar com destinos de runtime específicos.

## <a name="miscellaneous-performance-improvements"></a>Melhorias de desempenho diversas

As seguintes alterações na JVM têm um impacto direto no desempenho.

-   **JEP 197: Cache de Código Segmentado** \[[14](#ref14)\] – divide o cache de código em segmentos distintos. Essa segmentação fornece um melhor controle do volume de memória da JVM, diminui o tempo de verificação dos métodos compilados, diminui significativamente a fragmentação do cache de código e melhora o desempenho.

-   **JEP 254: Cadeias de caracteres compactas** \[[15](#ref15)\] – altera a representação interna de uma Cadeia de caracteres de dois bytes para um ou dois bytes por caractere, dependendo da codificação de caracteres. Como a maioria das cadeias contém caracteres ISO-8859-1/Latin-1, essa alteração efetivamente reduz a quantidade de espaço necessária para armazenar uma Cadeia de caracteres.

-   **JEP 310: Compartilhamento de Dados de Classe do Aplicativo** \[[16](#ref16)\] – o compartilhamento de dados de classe diminui o tempo de inicialização, permitindo que as classes arquivadas sejam mapeadas por memória no runtime. O Compartilhamento de Dados de Classe do Aplicativo estende o compartilhamento de dados de classe, permitindo que as classes de aplicativos sejam colocadas no arquivo de CDS. Quando várias JVMs compartilham o mesmo arquivo morto, a memória é salva e o tempo geral de resposta do sistema melhora.

-   **JEP 312: Handshakes Locais de Thread** \[[17](#ref17)\] – torna possível executar um retorno de chamada em threads sem executar um safepoint global da VM. Isso colabora para a VM atingir uma latência menor, reduzindo o número de safepoints globais.

-   **Alocação Lenta de Threads de Compilador** \[[18](#ref18)\] – no modo de compilação em camadas, a VM inicia um grande número de threads de compilador.
    Esse modo é o padrão em sistemas com muitas CPUs. Esses threads são criados independentemente da memória disponível ou do número de solicitações de compilação. Os threads consomem memória mesmo quando estão ociosos, ou seja, quase sempre, o que leva ao uso ineficiente de recursos. Para resolver esse problema, a implementação foi alterada para iniciar somente um thread de compilador de cada tipo durante a inicialização. Iniciar threads adicionais e desligar threads não utilizados é tratado dinamicamente. 

As alterações a seguir nas bibliotecas principais afetam o desempenho do código novo ou modificado.

-   **JEP 193: Identificadores Variáveis** \[[19](#ref19)\] – define um meio padrão para invocar os equivalentes de várias operações java.util.concurrent.atomic e sun.misc.Unsafe em campos de objeto e elementos de matriz, em um conjunto padrão de operações de limite para controle refinado de ordenação de memória e em uma operação padrão de limite de acessibilidade para garantir que um objeto referenciado permaneça fortemente acessível.

-   **JEP 269: Métodos de Fábrica de Conveniência para Coleções** \[[20](#ref20)\] – define APIs de biblioteca para tornar conveniente a criação de instâncias de coleções e mapas com pequenas quantidades de elementos. Os métodos de fábrica estáticos nas interfaces de coleção criam instâncias de coleção compactas e não modificáveis. Essas instâncias são inerentemente mais eficientes. As APIs criam coleções que são representadas de maneira compacta e não têm uma classe wrapper.

-   **JEP 285: Dicas de Espera de Rotação** \[[21](#ref21)\] – fornece uma API que permite ao Java sugerir ao sistema em tempo de execução que ele está em um loop de rotação. Certas plataformas de hardware se beneficiam da indicação do software de que um thread está em um estado de espera ativa.

-   **JEP 321: Cliente HTTP (Standard)** \[[22](#ref22)\] – fornece uma nova API de cliente HTTP que implementa HTTP/2 e WebSocket e pode substituir a API HttpURLConnection herdada.

## <a name="references"></a>Referências

<a id="ref1">\[1\]</a> Oracle Corporation, \"Notas sobre a versão do kit de desenvolvimento do Java 9,\" (online). Disponível: https://www.oracle.com/technetwork/java/javase/9u-relnotes-3704429.html.
(Acessado em 13 de novembro de 2019).

<a id="ref2">\[2\]</a> Oracle Corporation, \"Notas sobre a versão do kit de desenvolvimento do Java 10,\" (online). Disponível: https://www.oracle.com/technetwork/java/javase/10u-relnotes-4108739.html.
(Acessado em 13 de novembro de 2019).

<a id="ref3">\[3\]</a> Oracle Corporation, \"Notas sobre a versão do kit de desenvolvimento do Java 11,\" (online). Disponível: https://www.oracle.com/technetwork/java/javase/11u-relnotes-5093844.html.
(Acessado em 13 de novembro de 2019).

<a id="ref4">\[4\]</a> Oracle Corporation, \"Projeto Jigsaw,\" 22 de setembro de 
2017. (online). Disponível: http://openjdk.java.net/projects/jigsaw/.
(Acessado em 13 de novembro de 2019).

<a id="ref5">\[5\]</a> Oracle Corporation, \"JEP 328: Flight Recorder,\" 9 de setembro de 2018. (online). Disponível: http://openjdk.java.net/jeps/328. (Acessado em 13 de novembro de 2019).

<a id="ref6">\[6\]</a> Oracle Corporation, \"Mission Control,\" 25 de abril de 2019. (online). Disponível: https://wiki.openjdk.java.net/display/jmc/Main. (Acessado em 13 de novembro de 2019).

<a id="ref7">\[7\]</a> Oracle Corporation, \"JEP 158: Registro em log de JVM unificada,\" 14 de fevereiro de 2019. (online). Disponível: http://openjdk.java.net/jeps/158.
(Acessado em 13 de novembro de 2019).

<a id="ref8">\[8\]</a> Oracle Corporation, \"JEP 331: Criação de Perfil do Heap com Baixa Sobrecarga,\" 5 de setembro de 2018. (online). Disponível: http://openjdk.java.net/jeps/331. (Acessado em 13 de novembro de 2019).

<a id="ref9">\[9\]</a> Oracle Corporation, \"JEP 259: API de Movimentação de Pilha,\" 18 de julho de 2017. (online).
Disponível: http://openjdk.java.net/jeps/259. (Acessado em 13 de novembro de 2019).

<a id="ref10">\[10\]</a> Oracle Corporation, \"JEP 248: Torne o G1 o Coletor de Lixo Padrão,\" 12 de setembro de 2017. (online). Disponível: http://openjdk.java.net/jeps/248. (Acessado em 13 de novembro de 2019).

<a id="ref11">\[11\]</a> Oracle Corporation, \"JEP 318: Epsilon: Um Coletor de Lixo Não Operacional,\" 24 de setembro de 2018.
(online). Disponível: http://openjdk.java.net/jeps/318. (Acessado em 13 de novembro de 2019).

<a id="ref12">\[12\]</a> Oracle Corporation, \"JDK-8146115: Como melhorar o uso de detecção de contêineres e de configuração de recursos do Docker,\" 16 de setembro de 2019.
(online). Disponível: https://bugs.java.com/bugdatabase/view\_bug.do?bug\_id=JDK-8146115.
(Acessado em 13 de novembro de 2019).

<a id="ref13">\[13\]</a> Oracle Corporation, \"JEP 238: Arquivos JAR de Várias Versões,\" 22 de junho de 2017. (online). Disponível: http://openjdk.java.net/jeps/238. (Acessado em 13 de novembro de 2019).

<a id="ref14">\[14\]</a> Oracle Corporation, \"JEP 197: Cache de Código Segmentado,\" 28 de abril de 2017. (online).
Disponível: http://openjdk.java.net/jeps/197. (Acessado em 13 de novembro de 2019).

<a id="ref15">\[15\]</a> Oracle Corporation, \"JEP 254: Compactar Cadeias de Caracteres,\" 18 de maio de 2019. (online). Disponível: http://openjdk.java.net/jeps/254.
(Acessado em 13 de novembro de 2019).

<a id="ref16">\[16\]</a> Oracle Corporation, \"JEP 310: Compartilhamento de Dados de Classe do Aplicativo,\" 17 de agosto de 2018. (online). Disponível: https://openjdk.java.net/jeps/310. (Acessado em 13 de novembro de 2019).

<a id="ref17">\[17\]</a> Oracle Corporation, \"JEP 312: Handshakes Locais de Thread,\" 21 de agosto de 2019.
(online). Disponível: https://openjdk.java.net/jeps/312. (Acessado em 13 de novembro de 2019).

<a id="ref18">\[18\]</a> Oracle Corporation, \"JDK-8198756: Alocação Lenta de Threads de Compilador,\" 29 de outubro de 2018. (online). Disponível: https://bugs.java.com/bugdatabase/view\_bug.do?bug\_id=8198756.
(Acessado em 13 de novembro de 2019).

<a id="ref19">\[19\]</a> Oracle Corporation, \"JEP 193: Identificadores Variáveis,\" 17 de agosto de 2017. (online). Disponível: https://openjdk.java.net/jeps/193. (Acessado em 13 de novembro de 2019).

<a id="ref20">\[20\]</a> Oracle Corporation, \"JEP 269: Métodos de Fábrica de Conveniência para Coleções,\" 26 de junho de 2017. (online). Disponível: https://openjdk.java.net/jeps/269.
(Acessado em 13 de novembro de 2019).

<a id="ref21">\[21\]</a> Oracle Corporation, \"JEP 285: Dicas de Espera de Rotação,\" 20 de agosto de 2017. (online). Disponível: https://openjdk.java.net/jeps/285. (Acessado em 13 de novembro de 2019).

<a id="ref22">\[22\]</a> Oracle Corporation, \"JEP 321: Cliente HTTP (Standard),\" 27 de setembro de 2018. (online).
Disponível: https://openjdk.java.net/jeps/321. (Acessado em 13 de novembro de 2019).
