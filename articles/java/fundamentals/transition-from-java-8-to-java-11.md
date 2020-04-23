---
title: Transição do Java 8 para o Java 11
titleSuffix: Azure
description: Um guia para gerenciar a migração do Java 8 para o Java 11.
author: dsgrieve
manager: maverbur
tags: java
ms.service: azure
ms.devlang: java
ms.topic: article
ms.date: 11/19/2019
ms.author: dagrieve
ms.openlocfilehash: 528b111e945bb68bd18c849847522a070259c0f3
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81670612"
---
# <a name="transition-from-java-8-to-java-11"></a>Transição do Java 8 para o Java 11

Não há uma solução única para fazer a transição de código do Java 8 para o Java 11.
Para um aplicativo incomum, a migração do Java 8 para o Java 11 pode ser significativamente trabalhoso. Eventuais problemas incluem uma API removida, pacotes preteridos, o uso de uma API interna, alterações em balanceadores de classe e alterações na coleta de lixo. 

Em geral, as abordagens são tentar executar no Java 11 sem recompilação ou compilar com o JDK 11 primeiro. Se a meta for colocar um aplicativo em funcionamento o mais rápido possível, tentar executar no Java 11 geralmente será a melhor abordagem. Para uma biblioteca, a meta será publicar um artefato compilado e testado com o JDK 11.

A mudança para o Java 11 vale o esforço. Novos recursos foram adicionados e aprimoramentos foram feitos desde o Java 8. Esses recursos e aprimoramentos melhoram a inicialização, o desempenho e o uso de memória, além de fornecer melhor integração com contêineres. Também há adições e modificações à API que aprimoram a produtividade do desenvolvedor. 

Esse documento aborda ferramentas para inspecionar o código. Ele também aborda problemas que você pode encontrar e recomendações para resolvê-los. Você também deve consultar outros guias, como o [Guia de Migração do Oracle JDK](https://docs.oracle.com/en/java/javase/11/migrate/index.html). Como tornar o código existente [modular](http://openjdk.java.net/projects/jigsaw) não é abordado aqui.  


## <a name="the-toolbox"></a>A caixa de ferramentas

O Java 11 tem duas ferramentas, *jdeprscan* e *jdeps*, que são úteis para detectar eventuais problemas. Essas ferramentas podem ser executadas na classe existente ou em arquivos jar. Você pode avaliar o esforço de transição sem precisar recompilar. 

O [jdeprscan](https://docs.oracle.com/en/java/javase/11/tools/jdeprscan.html) procura o uso de uma API preterida ou removida.
O uso de uma API preterida não é um problema de bloqueio, mas algo a ser analisado. Há um arquivo jar atualizado? Você precisa registrar um problema em log para resolver o uso de uma API preterida? O uso de uma API preterida é um problema de bloqueio que precisa ser resolvido antes de você tentar a execução no Java 11.


[jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html), que é um analisador de dependência de classe Java. Quando usado com a opção `--jdk-internals`, *jdeps* informa qual classe depende de qual API interna. Você pode continuar usando a API interna no Java 11, mas substituir o uso deverá ser uma prioridade. A página Wiki OpenJDK [Ferramenta de Análise de Dependência do Java](https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool) tem substituições recomendadas para algumas APIs internas do JDK comumente usadas. 

Há plug-ins *jdeps* e *jdeprscan* para Gradle e Maven. Recomendamos adicionar essas ferramentas aos seus scripts de build. 

> [!div class="mx-tdBreakAll"]
> |Ferramenta|Plug-in Gradle|Plug-in Maven|
> |-|-|-|
> |jdeps|[jdeps-gradle-plugin](https://github.com/kordamp/jdeps-gradle-plugin)|[Plug-in JDeps Maven Apache](https://maven.apache.org/plugins/maven-jdeps-plugin/index.html)|
> |jdeprscan|[jdeprscan-gradle-plugin](https://github.com/kordamp/jdeprscan-gradle-plugin)|[Plug-in JDeprScan Maven Apache](https://maven.apache.org/plugins/maven-jdeprscan-plugin/index.html)|

O compilador Java propriamente dito, *javac*, é outra ferramenta em sua caixa de ferramentas. Os avisos e erros que você receber de *jdeprscan* e *jdeps* serão provenientes do compilador.  A vantagem de usar *jdeprscan* e *jdeps* é que você pode executar essas ferramentas em arquivos de classe e jars existentes, incluindo bibliotecas de terceiros.

O que *jdeprscan* e *jdeps* não podem fazer é alertar sobre o uso de reflexão para acessar uma API encapsulada. O acesso reflexivo é verificado em runtime. Por último, você precisa executar o código no Java 11 para saber com certeza.

### <a name="using-jdeprscan"></a>Usar jdeprscan

A maneira mais fácil de usar [jdeprscan](https://docs.oracle.com/en/java/javase/11/tools/jdeprscan.html) é fornecer a ele um arquivo jar de um build existente. Você também pode fornecer um diretório, como o diretório de saída do compilador, ou um nome de classe individual. Use a opção `--release 11` para obter a lista mais completa de APIs preteridas. Se desejar priorizar qual API preterida procurar, volte a configuração para `--release 8`. A API preterida no Java 8 provavelmente será removida antes do que a API que foi preterida mais recentemente. 

```console
jdeprscan --release 11 my-application.jar
```

A ferramenta *jdeprscan* gerará uma mensagem de erro se tiver problemas para resolver uma classe dependente.
Por exemplo, `error: cannot find class org/apache/logging/log4j/Logger`. Adicionar classes dependentes ao `--class-path` ou usar o class-path do aplicativo é recomendado, mas a ferramenta continuará a verificação sem ele.
O argumento é *&#8209;&#8209;class&#8209;path*. Nenhuma outra variação do argumento de class-path funcionará.

```console
jdeprscan --release 11 --class-path log4j-api-2.13.0.jar my-application.jar
error: cannot find class sun/misc/BASE64Encoder
class com/company/Util uses deprecated method java/lang/Double::<init>(D)V
```
Essa saída informa que a classe `com.company.Util` está chamando um construtor preterido da classe `java.lang.Double`. O javadoc recomendará a API a ser usada no lugar da API preterida. Nenhum volume de trabalho resolverá o `error: cannot find class sun/misc/BASE64Encoder` porque é a API que foi removida. Desde o Java 8, `java.util.Base64` deve ser usado. 

Execute `jdeprscan --release 11 --list` para ter uma ideia de qual API foi preterida desde o Java 8. Para obter uma lista de APIs que foram removidas, execute `jdeprscan --release 11 --list --for-removal`.

### <a name="using-jdeps"></a>Usar jdeps

Use [jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html), com a opção `--jdk-internals` para localizar dependências na API interna do JDK. A opção de linha de comando `--multi-release 11` é necessária para esse exemplo porque *log4j-core-2.13.0.jar* é um [arquivo jar de várias versões](https://docs.oracle.com/en/java/javase/11/docs/specs/jar/jar.html#multi-release-jar-files). Sem essa opção, *jdeps* reclamará se encontrar um arquivo jar de várias versões. A opção especifica qual versão de arquivos de classe inspecionar. 

```console
jdeps --jdk-internals --multi-release 11 --class-path log4j-core-2.13.0.jar my-application.jar
Util.class -> JDK removed internal API
Util.class -> jdk.base
Util.class -> jdk.unsupported
   com.company.Util        -> sun.misc.BASE64Encoder        JDK internal API (JDK removed internal API)
   com.company.Util        -> sun.misc.Unsafe               JDK internal API (jdk.unsupported)
   com.company.Util        -> sun.nio.ch.Util               JDK internal API (java.base)

Warning: JDK internal APIs are unsupported and private to JDK implementation that are
subject to be removed or changed incompatibly and could break your application.
Please modify your code to eliminate dependence on any JDK internal APIs.
For the most recent update on JDK internal API replacements, please check:
https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool

JDK Internal API                         Suggested Replacement
----------------                         ---------------------
sun.misc.BASE64Encoder                   Use java.util.Base64 @since 1.8
sun.misc.Unsafe                          See http://openjdk.java.net/jeps/260   

```

A saída oferece um bom conselho sobre como eliminar o uso da API interna do JDK! Quando possível, a API de substituição será sugerida. O nome do módulo em que o pacote está encapsulado é fornecido nos parênteses. O nome do módulo poderá ser usado com `--add-exports` ou `--add-opens` se for necessário [quebrar o encapsulamento](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-2F61F3A9-0979-46A4-8B49-325BA0EE8B66) explicitamente. 

O uso de *sun.misc.BASE64Encoder* ou *sun.misc.BASE64Decoder* resultará em um *java.lang.NoClassDefFoundError* no Java 11. O código que usa essas APIs precisa ser modificado para usar *java.util.Base64*. 

Tente eliminar o uso de qualquer API proveniente do módulo *jdk.unsupported*. A API deste módulo referenciará a [JEP (Proposta de Aprimoramento do JDK) 260](http://openjdk.java.net/jeps/260) como substituição sugerida.
Resumindo, a JEP 260 informa que haverá suporte para o uso de uma API interna até que a substituição da API esteja disponível. Embora seu código possa usar a API interna do JDK, ele continuará sendo executado, pelo menos temporariamente. Examine a JEP 260 pois ela aponta para substituições para alguma API interna. 
[Identificadores de variáveis](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/invoke/VarHandle.html) podem ser usados em lugar de alguma API *sun.misc.Unsafe*, por exemplo. 

*jdeps* pode fazer mais do que apenas verificar o uso de elementos internos do JDK. É uma ferramenta útil para analisar dependências e gerar arquivos de informações sobre módulos. Examine a [documentação](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html) para saber mais.

### <a name="using-javac"></a>Usar javac

A compilação com o JDK 11 exigirá que atualizações criem scripts, ferramentas, estruturas de teste e bibliotecas incluídas. Use a opção `-Xlint:unchecked` para *javac* para obter os detalhes sobre o uso de uma API interna do JDK e outros avisos. Pode ser necessário usar `--add-opens` ou `--add-reads` para expor pacotes encapsulados ao compilador (confira a [JEP 261](http://openjdk.java.net/jeps/261)). 

As bibliotecas podem considerar o empacotamento como um [arquivo jar de várias versões](https://docs.oracle.com/en/java/javase/11/docs/specs/jar/jar.html#multi-release-jar-files). Arquivos jar de várias versões permitem que você dê suporte a tempos de execução do Java 8 e Java 11 do mesmo arquivo jar. Eles adicionam complexidade ao build. Como criar jars de várias versões vai além do escopo deste documento. 

## <a name="running-on-java-11"></a>Executar no Java 11

A maioria dos aplicativos deve ser executada no Java 11 sem modificação. A primeira coisa a se tentar é executar no Java 11 sem recompilar o código. A questão de apenas executar é ver quais avisos e erros são provenientes da execução. Essa abordagem obtém um  
aplicativo para ser executado no Java 11 de maneira mais rápida concentrando-se no mínimo que precisa ser feito. 

A maioria dos problemas que você pode encontrar pode ser resolvida sem precisar recompilar o código.
Se um problema precisar ser corrigido no código, corrija-o mas continue compilando com o JDK 8. Se possível, trabalhe para fazer o aplicativo ser *executado* com a versão 11 do `java` antes de *compilar* com o JDK 11. 

### <a name="check-command-line-options"></a>Verificar as opções de linha de comando

Antes de executar no Java 11, faça uma rápida verificação das opções de linha de comando. 
As [opções que foram removidas](#unrecognized-options) farão a JVM (Máquina Virtual Java) ser encerrada. Essa verificação é especialmente importante se você usa opções de registro em log de GC, pois elas mudaram drasticamente após o Java 8. A ferramenta [JaCoLine](https://jacoline.dev/about) é boa para ser usada para detectar problemas com opções de linha de comando. 

### <a name="check-third-party-libraries"></a>Verificar bibliotecas de terceiros

Uma fonte potencial de problemas são bibliotecas de terceiros que você não controla. Você pode atualizar proativamente bibliotecas de terceiros para versões mais recentes. Ou você pode ver o que está fora da execução do aplicativo a atualizar apenas essas bibliotecas necessárias. O problema com a atualização de todas as bibliotecas para uma versão recente é que isso tornará mais difícil encontrar a causa raiz se houver algum erro no aplicativo. O erro aconteceu por causa de alguma biblioteca atualizada? Ou o erro foi causado por alguma alteração no runtime? O problema com a atualização apenas do que é necessário é que isso pode levar várias iterações para ser resolvido.

A recomendação aqui é fazer o mínimo de alterações possível e atualizar bibliotecas de terceiros como um [esforço separado](#next-steps). Se você atualizar uma biblioteca de terceiros, na maioria dos casos, você desejará a versão mais recente e maior compatível com o Java 11. Dependendo do quanto sua versão atual é anterior a essa, talvez convenha adotar uma abordagem mais cautelosa e atualizar para a primeira versão compatível com Java 9+. 

Além de examinar notas sobre a versão, você pode usar *jdeps* e *jdeprscan* para avaliar o arquivo jar. Além disso, o Grupo de Qualidade do OpenJDK mantém uma página Wiki [Atendimento de Qualidade](https://wiki.openjdk.java.net/display/quality/Quality+Outreach) que lista o status de teste de muitos projetos FOSS (Software Livre Gratuito) em versões do OpenJDK. 

### <a name="explicitly-set-garbage-collection"></a>Definir explicitamente a coleta de lixo

O Parallel GC (coletor de lixo paralelo) é o GC padrão no Java 8. Se o aplicativo estiver usando o padrão, o GC deverá ser definido explicitamente com a opção de linha de comando `-XX:+UseParallelGC`.
O padrão mudou no Java 9 para o G1GC (coletor de lixo Garbage First). Para fazer uma comparação justa de um aplicativo em execução no Java 8 versus no Java 11, as configurações de GC devem ser as mesmas. O experimento com as configurações do GC deve ser adiado até que o aplicativo tenha sido validado no Java 11. 

### <a name="explicitly-set-default-options"></a>Definir explicitamente as opções padrão

Se estiver em execução na VM HotSpot, definir a opção de linha de comando `-XX:+PrintCommandLineFlags` despejará os valores de opções definidos pela VM, particularmente os padrões definidos pelo GC.
Execute esse sinalizador no Java 8 e use as opções impressas ao executar no Java 11. Na maior parte dos casos, os padrões são os mesmos da versão 8 à versão 11. No entanto, o uso das configurações da versão 8 garante paridade.

É recomendável definir a opção de linha de comando `--illegal-access=warn`.
No Java 11, usar a reflexão para acessar a API interna do JDK resultará em um [aviso de acesso reflexivo ilícito](#warning-an-illegal-reflective-access-operation-has-occurred).
Por padrão, o aviso só é emitido no primeiro acesso ilícito. Definir `--illegal-access=warn` causará um aviso em *todo* acesso reflexivo ilícito. Você encontrará mais casos se o acesso ilícito com a opção estiver definido como *avisar*. Contudo, você também receberá muitos avisos redundantes.  
Depois que o aplicativo for executado no Java 11, defina `--illegal-access=deny` para imitar o comportamento futuro do runtime do Java. Do Java 16 em diante, o padrão será `--illegal-access=deny`. 

### <a name="classloader-cautions"></a>Cuidados do ClassLoader

No Java 8, você pode converter o carregador de classe de sistema em um `URLClassLoader`. Isso geralmente é feito por aplicativos e bibliotecas que desejam injetar classes no classpath em runtime. A hierarquia do carregador de classes mudou no Java 11. O carregador de classe de sistema (também conhecido como o carregador de classe de aplicativo) agora é uma classe interna. A conversão em um `URLClassLoader` gerará um `ClassCastException` em runtime. O Java 11 não tem uma API para ampliar dinamicamente o classpath em runtime, mas isso pode ser feito por meio de reflexão, com advertências óbvias sobre como usar a API interna. 

No Java 11, o carregador de classe de inicialização só carrega os módulos principais. Se você criar um carregador de classe com um pai nulo, ele poderá não encontrar todas as classes da plataforma. No Java 11, você precisa passar `ClassLoader.getPlatformClassLoader()` em vez de `null` como o carregador de classe pai nesses casos. 

### <a name="locale-data-changes"></a>Alterações nos dados de localidade

A origem padrão dos dados de localidade no Java 11 foi alterada com a [JEP 252](http://openjdk.java.net/jeps/252) para o Repositório de Dados de Localidade Comum do Consórcio Unicode. Isso pode afetar a formatação localizada. Defina a propriedade do sistema `java.locale.providers=COMPAT,SPI` para reverter para o comportamento de localidade do Java 8, se necessário. 

### <a name="potential-issues"></a>Possíveis problemas

Veja alguns problemas comuns que você poderá ter. Siga os links para obter mais detalhes sobre esses problemas.

- [Opção de VM não reconhecida](#unrecognized-options)
- [Opção não reconhecida](#unrecognized-options)
- [Aviso da VM: ignorando a opção](#vm-warnings)
- [Aviso da VM: A opção &lt;*option*&gt; foi preterida](#vm-warnings)
- [AVISO: ocorreu uma operação de acesso reflexivo ilícito](#warning-an-illegal-reflective-access-operation-has-occurred)
- [java.lang.reflect.InaccessibleObjectException](#javalangreflectinaccessibleobjectexception)
- [java.lang.NoClassDefFoundError](#javalangnoclassdeffounderror)
- [-Xbootclasspath/p não é mais uma opção com suporte](#-xbootclasspathp-is-no-longer-a-supported-option)
- [java.lang.UnsupportedClassVersionError](#unsupportedclassversionerror)

#### <a name="unrecognized-options"></a>Opções não reconhecidas

Se uma opção de linha de comando tiver sido removida, o aplicativo imprimirá `Unrecognized option:` ou `Unrecognized VM option` seguido pelo nome da opção inválida. Uma opção não reconhecida fará a VM ser encerrada.
As opções que foram preteridas, mas não removidas, produzirão um [aviso de VM](#vm-warnings).

Em geral, as opções que foram removidas não têm substituição e o único recurso é remover a opção da linha de comando. A exceção são opções para registro de coleta de lixo em log. O registro de GC em log foi [reimplementado](http://openjdk.java.net/jeps/271) no Java 9 para usar a [estrutura unificada de registro em log da JVM](http://openjdk.java.net/jeps/158). Consulte a "Tabela 2-2 Mapear Sinalizadores de Registro de Coleta de Lixo Herdada em Log para a Configuração Xlog" na seção [Habilitar o Registro em Log com a Estrutura Unificada de Registro em Log da JVM](https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-BE93ABDC-999C-4CB5-A88B-1994AAAC74D5) da Referência de Ferramentas do Java SE 11. 

#### <a name="vm-warnings"></a>Avisos da VM

O uso de opções preteridas produzirá um aviso. Uma opção é preterida quando foi substituída ou não é mais útil. Como ocorre com [opções removidas](#unrecognized-options), essas opções devem ser removidas da linha de comando.
O aviso `VM Warning: Option <option> was deprecated` significa que ainda há suporte para a opção, mas que o suporte pode ser removido no futuro. Uma opção que não tem mais suporte e que gerará o aviso `VM Warning: Ignoring option`.
As opções que não têm mais suporte não têm efeito sobre o runtime.

A página da Web [Explorador de Opções da VM](https://chriswhocodes.com/hotspot_option_differences.html) fornece uma lista exaustiva de opções que foram adicionadas ao Java ou removidas dele desde o JDK 7. 

#### <a name="error-could-not-create-the-java-virtual-machine"></a>Erro: não foi possível criar a Máquina Virtual Java

Essa mensagem de erro é impressa quando a JVM encontra uma [opção não reconhecida](#unrecognized-options).

#### <a name="warning-an-illegal-reflective-access-operation-has-occurred"></a>AVISO: ocorreu uma operação de acesso reflexivo ilícito

Quando o código Java usa a reflexão para acessar a API interna do JDK, o runtime emitirá um aviso de acesso reflexivo ilícito.

```console
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by my.sample.Main (file:/C:/sample/) to method sun.nio.ch.Util.getTemporaryDirectBuffer(int)
WARNING: Please consider reporting this to the maintainers of com.company.Main
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
```

O que isso significa é que um módulo não exportou o pacote que está sendo acessado por meio de reflexão. O pacote é *encapsulado* no módulo e é, basicamente, a API interna. O aviso pode ser ignorado como um primeiro esforço para entrar em funcionamento no Java 11.
O runtime do Java 11 permite o acesso reflexivo para que o código herdado possa continuar funcionando.  

Para resolver esse aviso, procure um código atualizado que não utilize a API interna. Se não for possível resolver o problema com o código atualizado, a opção de linha de comando `--add-exports` ou `--add-opens` poderá ser usada para abrir o acesso ao pacote.
Essas opções permitem acesso a tipos não exportados de um módulo com base em outro módulo.

A opção [`--add-exports`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-2F61F3A9-0979-46A4-8B49-325BA0EE8B66) permite que o módulo de destino acesse os tipos *public* do pacote nomeado do módulo de origem. Às vezes, o código usará `setAccessible(true)` para acessar membros e API não públicos. Isso é conhecido como *reflexão profunda*. Nesse caso, use [`--add-opens`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-12F945EB-71D6-46AF-8C3D-D354FD0B1781) para oferecer ao seu código acesso aos membros não públicos de um pacote. Se você não tiver certeza quanto a usar *--add-exports* ou *--add-opens*, comece com *--add-exports*. 

As opções `--add-exports` ou `--add-opens` devem ser consideradas como uma solução alternativa, não uma solução de longo prazo.
O uso dessas opções quebra o encapsulamento do sistema de módulo, o que deve impedir a API interna do JDK de ser usada.  Se a API interna for removida ou alterada, o aplicativo falhará.  O acesso reflexivo será negado no Java 16, exceto quando o acesso tiver sido habilitado por opções de linha de comando como `--add-opens`.
Para imitar o comportamento futuro, defina `--illegal-access=deny` na linha de comando.

O aviso no exemplo acima é emitido porque o pacote `sun.nio.ch` não é exportado pelo módulo `java.base`. Em outras palavras, não há nenhum `exports sun.nio.ch;` no arquivo `module-info.java` do módulo `java.base`. Isso pode ser resolvido com `--add-exports=java.base/sun.nio.ch=ALL-UNNAMED`. Classes não definidas em um módulo pertencem implicitamente ao módulo *sem nome*, literalmente chamado de `ALL-UNNAMED`.

#### <a name="javalangreflectinaccessibleobjectexception"></a>java.lang.reflect.InaccessibleObjectException

Essa exceção indica que você está tentando chamar `setAccessible(true)` em um campo ou método de uma classe encapsulada. Você também pode receber um [aviso de acesso reflexivo ilícito](#warning-an-illegal-reflective-access-operation-has-occurred). Use a opção [`--add-opens`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-12F945EB-71D6-46AF-8C3D-D354FD0B1781) para fornecer ao seu código acesso aos membros não públicos de um pacote. A mensagem de exceção informará que o módulo “não abre” o pacote para o módulo que está tentando chamar *setAccessible*. Se o módulo for "sem nome", use `UNNAMED-MODULE` como o módulo de destino na opção *--add-opens*.

```shell
java.lang.reflect.InaccessibleObjectException: Unable to make field private final java.util.ArrayList jdk.internal.loader.URLClassPath.loaders accessible: 
module java.base does not "opens jdk.internal.loader" to unnamed module @6442b0a6

$ java --add-opens=java.base/jdk.internal.loader=UNNAMED-MODULE example.Main
```

#### <a name="javalangnoclassdeffounderror"></a>java.lang.NoClassDefFoundError

*NoClassDefFoundError* é mais provavelmente causado por um pacote dividido ou referenciando módulos removidos. 

##### <a name="noclassdeffounderror-caused-by-split-packages"></a>NoClassDefFoundError causado por pacotes divididos

Um pacote dividido é quando um pacote é encontrado em mais de uma biblioteca. O sintoma de um problema de pacote dividido é que uma classe que você sabe que está no class-path não é encontrada. 

Esse problema só ocorrerá ao usar o module-path. O sistema do módulo Java otimiza a pesquisa de classe restringindo um pacote a um módulo *nomeado*. O runtime dá preferência ao module-path sobre o class-path ao realizar uma pesquisa de classe. Se um pacote estiver dividido entre um módulo e o class-path, apenas o módulo será usado para realizar a pesquisa de classe. Isso pode causar erros de `NoClassDefFound`. 

Uma maneira fácil de verificar se há um pacote dividido é conectar seu caminho de módulo e de classe ao [jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html) e usar o caminho para o arquivos de classe de aplicativo como o &lt;path&gt;. Se houver um pacote dividido, jdeps imprimirá um aviso: `Warning: split package: <package-name> <module-path> <split-path>`. 

Esse problema pode ser resolvido usando `--patch-module <module-name>=<path>[,<path>]` para adicionar o pacote dividido no módulo nomeado. 

##### <a name="noclassdeffounderror-caused-by-using-java-ee-or-corba-modules"></a>NoClassDefFoundError causado usando módulos Java EE ou CORBA

Se o aplicativo for executado no Java 8, mas gerar um `java.lang.NoClassDefFoundError` ou um `java.lang.ClassNotFoundException`, então provavelmente o aplicativo estará usando um pacote dos módulos Java EE ou CORBA. Esses módulos foram preteridos no Java 9 e [removidos no Java 11](https://openjdk.java.net/jeps/320). 

Para resolver o problema, adicione uma dependência de runtime ao seu projeto.

> [!div class="mx-tdBreakAll"]
> |Módulo removido|Pacote afetado|Dependência sugerida|
> |-|-|-|
> |JAX-WS (API Java para Serviços Web XML) |java.xml.ws |[Runtime do RI JAX WS](https://mvnrepository.com/artifact/com.sun.xml.ws/jaxws-rt) |
> |JAXB (Arquitetura Java para Associação XML) |java.xml.bind |[Runtime do JAXB](https://mvnrepository.com/artifact/org.glassfish.jaxb/jaxb-runtime)|
> |JAV (Estrutura de Ativação do JavaBeans) |java.activation |[Estrutura de Ativação do JavaBeans (TM)](https://mvnrepository.com/artifact/javax.activation/activation) |
> |Anotações comuns |java.xml.ws.annotation |[API de Anotação do Javax](https://mvnrepository.com/artifact/javax.annotation/javax.annotation-api)|
> |CORBA (Arquitetura do Agente de Solicitação de Objeto Comum) |java.corba | [GlassFish CORBA ORB](https://mvnrepository.com/artifact/org.glassfish.corba/glassfish-corba-orb) |
> |JTA (API de Transação do Java) |java.transaction | [API de Transação do Java](https://mvnrepository.com/artifact/javax.transaction/jta)|

#### <a name="-xbootclasspathp-is-no-longer-a-supported-option"></a>-Xbootclasspath/p não é mais uma opção com suporte

O suporte para `-Xbootclasspath/p` foi removido. Use `--patch-module` em vez disso. A opção *--patch-module* é descrita na [JEP 261](http://openjdk.java.net/jeps/261). Procure a seção rotulada “Aplicação de patch ao conteúdo do módulo”. *--patch-module* pode ser usado com *javac* e com *java* para substituir ou aumentar as classes em um módulo. 

O que *--patch-module* faz, na verdade, é inserir o módulo de patch na pesquisa de classe do sistema de módulo. O sistema de módulo pegará a classe do módulo de patch primeiro. Esse é o mesmo efeito que acrescentar o bootclasspath no Java 8. 

#### <a name="unsupportedclassversionerror"></a>UnsupportedClassVersionError

Essa exceção significa que você está tentando executar um código compilado com uma versão posterior do Java em uma versão anterior do Java. Por exemplo, você está executando no Java 11 com um jar que foi compilado com o JDK 13. 

| Versão Java | Versão de formato de arquivo de classe |
|-|-|
| 8  | 52 |
| 9  | 53 |
| 10 | 54 |
| 11 | 55 |
| 12 | 56 |
| 13 | 57 |

## <a name="next-steps"></a>Próximas etapas

Depois que o aplicativo for executado no Java 11, considere tirar as bibliotecas de class-path e passar para o module-path. Procure versões atualizadas das bibliotecas das quais seu aplicativo depende. Escolha bibliotecas modulares, se disponível. Use o module-path ao máximo, mesmo se você não planeja usar módulos em seu aplicativo. Usar o module-path tem melhor desempenho para o carregamento de classes do que o class-path. 
