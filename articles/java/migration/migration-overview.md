---
title: Migrar aplicativos Java para o Azure
description: Este tópico fornece uma visão geral das estratégias recomendadas para migrar aplicativos Java para o Azure.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.openlocfilehash: e6215502b54bedf62f40a024f9e7b3acc01cdc1f
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81670602"
---
# <a name="migrate-java-applications-to-azure"></a>Migrar aplicativos Java para o Azure

Este tópico fornece uma visão geral das estratégias recomendadas para migrar aplicativos Java para o Azure.

Essas diretrizes de migração foram projetadas para abranger cenários predominantes de Java no Azure e fornecer sugestões e considerações de planejamento de alto nível. Se desejar discutir um cenário de migração de aplicativo Java específico com a equipe do Microsoft Java no Azure, preencha o questionário a seguir e um representante entrará em contato com você.

> [!div class="nextstepaction"]
> [Questionário de migração de Java](https://aka.ms/migrate-my-Java-app-requested-thru-docs)

## <a name="identifying-application-type"></a>Identificar o tipo do aplicativo

Antes de selecionar um destino de nuvem para seu aplicativo Java, você precisará identificar qual é o tipo do aplicativo. A maioria dos aplicativos Java são de um dos seguintes tipos:

* [Aplicativos do Spring Boot/JAR](#spring-boot--jar-applications)
* [Spring Cloud/microsserviços](#spring-cloud--microservices)
* [Aplicativos Web](#web-applications)
* [Aplicativos Java EE](#java-ee-applications)
* [Trabalhos agendados/em lotes](#batch--scheduled-jobs)

Esses tipos são descritos nas seções a seguir.

### <a name="spring-boot--jar-applications"></a>Aplicativos do Spring Boot/JAR

Muitos aplicativos mais recentes são invocados diretamente da linha de comando. Esses aplicativos ainda manipulam solicitações da Web, mas em vez de depender de um servidor de aplicativos para fornecer manipulação de solicitações HTTP, eles incorporam a comunicação HTTP e todas as outras dependências diretamente no pacote de aplicativos. Esses aplicativos são frequentemente criados com estruturas como Spring Boot, Dropwizard, Micronaut, MicroProfile, Vert.x e outros.

Esses aplicativos são empacotados em arquivos com a extensão *.jar* (arquivos JAR).

### <a name="spring-cloud--microservices"></a>Spring Cloud/microsserviços

O estilo arquitetônico de microserviço é uma abordagem para desenvolver um único aplicativo como um pacote de pequenos serviços, cada um executando em seu próprio processo e se comunicando com mecanismos leves, geralmente uma API de recurso HTTP. Esses serviços são criados com base em recursos de negócios e são implantáveis independentemente por um maquinário de implantação totalmente automatizado. Há um mínimo de gerenciamento centralizado desses serviços, que pode ser escrito em linguagens de programação diferentes e usar tecnologias de armazenamento de dados diferentes. Esses serviços são frequentemente criados com estruturas como o Spring Cloud.

Esses serviços são empacotados em vários aplicativos com a extensão *.jar* (arquivos JAR).

### <a name="web-applications"></a>Aplicativos Web

Os aplicativos Web são executados dentro de um contêiner [Servlet](https://en.wikipedia.org/wiki/Java_servlet). Alguns usam APIs de servlet diretamente, enquanto muitos usam estruturas adicionais que encapsulam APIs de servlet, como Apache Struts, Spring MVC, JSF (JavaServer Faces) e outros.

Aplicativos Web são empacotados em arquivos com a extensão *.war* (arquivos WAR).

### <a name="java-ee-applications"></a>Aplicativos Java EE

Aplicativos Java EE (também conhecidos como aplicativos J2EE ou, mais recentemente, aplicativos Jakarta EE) podem conter alguns, todos ou nenhum dos elementos de aplicativos Web. Eles também podem conter e consumir muitos componentes adicionais, conforme definido pela [especificação Java EE](https://en.wikipedia.org/wiki/Java_Platform,_Enterprise_Edition).

Os aplicativos Java EE podem ser empacotados como arquivos com a extensão *.ear* (arquivos EAR) ou como arquivos com a extensão *.war* (arquivos WAR).

Os aplicativos Java EE devem ser implantados em servidores de aplicativos compatíveis com Java EE (como WebLogic, WebSphere, WildFly, GlassFish, Payara e outros).

Os aplicativos que dependem apenas dos recursos fornecidos pela especificação Java EE (ou seja, aplicativos independentes de servidor de aplicativo) podem ser migrados de um servidor de aplicativos que esteja em conformidade para outro. Se o aplicativo for dependente de um servidor de aplicativos específico (dependente de servidor de aplicativos), talvez seja necessário selecionar um destino do serviço do Azure que permita hospedar o servidor de aplicativos.

### <a name="batch--scheduled-jobs"></a>Trabalhos agendados/em lotes

Alguns aplicativos devem ser executados brevemente, executar uma carga de trabalho específica e, em seguida, sair em vez de esperar por solicitações ou entrada do usuário. Às vezes, esses trabalhos precisam ser executados uma vez ou em intervalos regulares agendados. No local, esses trabalhos são geralmente invocados do crontab de um servidor.

Esses aplicativos são empacotados em arquivos com a extensão *.jar* (arquivos JAR).

> [!NOTE]
> Se o aplicativo usar um agendador (como o Spring Batch ou o Quartz) para executar tarefas agendadas, é altamente recomendável que você fatore essas tarefas para serem executadas fora do aplicativo. Se seu aplicativo for dimensionado para várias instâncias na nuvem, o mesmo trabalho será executado mais de uma vez. Além disso, se o mecanismo de agendamento usar o fuso horário local do host, você poderá enfrentar um comportamento indesejável desse mecanismo ao dimensionar seu aplicativo entre regiões.

## <a name="selecting-the-target-azure-service-destination"></a>Selecionar o destino do serviço do Azure de destino

As seções a seguir mostram quais destinos de serviço atendem aos requisitos do aplicativo e quais responsabilidades eles envolvem.

### <a name="feature-grid"></a>Grade de recursos

Use a grade a seguir para identificar os destinos que dão suporte aos tipos de aplicativos e recursos necessários.

|   |Aplicativo<br>Serviço<br>Java SE|Aplicativo<br>Serviço<br>Tomcat|Azure<br>Spring<br>Nuvem|AKS|Máquinas Virtuais|
|---|---|---|---|---|---|---|
| Aplicativos do Spring Boot/JAR                                    |&#x2714;|        |        |&#x2714;|&#x2714;|
| Spring Cloud/microsserviços                                      |        |        |&#x2714;|&#x2714;|&#x2714;|
| Aplicativos Web                                                  |        |&#x2714;|        |&#x2714;|&#x2714;|
| Aplicativos Java EE                                              |        |        |        |&#x2714;|&#x2714;|
| Servidores de aplicativos comerciais<br>(como WebLogic ou WebSphere) |        |        |        |&#x2714;|&#x2714;|
| Persistência de longo prazo no sistema de arquivos local                         |&#x2714;|&#x2714;|        |&#x2714;|&#x2714;|
| Clustering no nível do servidor de aplicativos                               |        |        |        |&#x2714;|&#x2714;|
| Trabalhos agendados/em lotes                                            |        |        |&#x2714;|&#x2714;|&#x2714;|

### <a name="ongoing-responsibility-grid"></a>Grade de responsabilidade em andamento

Use a grade a seguir para entender a responsabilidade que cada destino coloca sobre a sua equipe após a migração.

A sua equipe é responsável continuamente pelas tarefas indicadas com "&#x1F449;". É recomendável implementar um processo robusto e altamente automatizado para atender a todas essas responsabilidades. 

> [!NOTE]
> Essa não é uma lista exaustiva de responsabilidades.

|   | Serviço de Aplicativo | Azure Spring Cloud | AKS | Máquinas Virtuais |
|---|---|---|---|---|
| Atualizando bibliotecas<br>(incluindo a correção de vulnerabilidades)                 | &#x1F449;   | &#x1F449;   | &#x1F449;   | &#x1F449; |
| Atualizando o servidor de aplicativos<br>(incluindo a correção de vulnerabilidades)    | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Atualizando o Java Runtime<br>(incluindo a correção de vulnerabilidades)          | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Disparando atualizações do Kubernetes<br>(executado pelo Azure com um gatilho manual) | N/D         | N/D         | &#x1F449;   | N/D       |
| Reconciliar alterações de API de Kubernetes não compatíveis com versões anteriores                  | N/D         | N/D         | &#x1F449;   | N/D       |
| Atualizar imagem base do contêiner<br>(incluindo a correção de vulnerabilidades)      | N/D         | N/D         | &#x1F449;   | N/D       |
| Atualizar o sistema operacional<br>(incluindo a correção de vulnerabilidades)      | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Detectar e reiniciar instâncias com falha                                   | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Implementar a reinicialização sem interrupção e com descarga para atualizações                       | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Gerenciamento de infraestrutura                                                   | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Monitoramento e gerenciamento de alertas                                             | &#x1F449;   | &#x1F449;   | &#x1F449;   | &#x1F449; |

Se você implantar o contêiner do servlet (como o Spring Boot) como parte de seu aplicativo, deverá considerá-lo uma biblioteca e, como tal, sendo sempre sua responsabilidade.

## <a name="ensuring-on-premises-connectivity"></a>Garantir a conectividade local

Se seu aplicativo precisar acessar qualquer um dos seus serviços locais, você precisará provisionar um dos serviços de conectividade do Azure. Para obter mais informações, confira [Escolher uma solução para conectar uma rede local ao Azure](/azure/architecture/reference-architectures/hybrid-networking/). Como alternativa, você precisará refatorar seu aplicativo para usar APIs disponíveis publicamente que seus recursos locais expõem.

Você precisa concluir esse esforço antes de iniciar qualquer migração.

## <a name="inventory-current-capacity-and-resource-usage"></a>Capacidade atual de inventário e uso de recursos

Documente o hardware dos servidores de produção atuais mais as contagens de solicitação média e de pico e o uso de recursos. Você precisará dessas informações para provisionar recursos no destino do serviço.

## <a name="migration-guidance"></a>Guia de migração

Use as grades a seguir para encontrar diretrizes de migração por tipo de aplicativo e por destino de serviço do Azure direcionado.

**Aplicativos Java**

Use as linhas abaixo para encontrar o tipo do seu aplicativo Java e as colunas para localizar o destino do serviço do Azure que hospedará seu aplicativo.

Se você desejar migrar um aplicativo JBoss EAP para o Tomcat no Serviço de Aplicativo, primeiro converta o aplicativo Java EE em aplicativos Web Java (servlets) em execução no Tomcat e siga as diretrizes indicadas abaixo.

Se você desejar migrar um aplicativo Web no Tomcat para o Azure Spring Cloud, primeiro converta o aplicativo em microsserviços do Spring Cloud e siga as diretrizes indicadas abaixo.

|Destino&nbsp;→<br><br>Tipo&nbsp;do Aplicativo&nbsp;↓|Aplicativo<br>Serviço<br>Java SE|Aplicativo<br>Serviço<br>Tomcat|Azure<br>Spring<br>Nuvem|AKS|Máquinas Virtuais|
|---|---|---|---|---|---|---|
| Spring Boot/<br>Aplicativos JAR | [diretrizes][5] | diretrizes<br>planejado | diretrizes<br>planejado | diretrizes<br>planejado | diretrizes<br>planejado |
| Spring Cloud/<br>microsserviços   | N/D           | N/D                 | diretrizes<br>planejado | diretrizes<br>planejado | diretrizes<br>planejado |
| Aplicativos Web<br>no Tomcat     | N/D           | [diretrizes][2]       | N/D                 | [diretrizes][3]       | diretrizes<br>planejado |

**Aplicativos Java EE**

Use as linhas abaixo para localizar o tipo de aplicativo Java EE em execução em um servidor de aplicativo específico. Use as colunas para localizar o destino do serviço do Azure que hospedará seu aplicativo.

|Destino&nbsp;→<br><br>Servidor de aplicativo&nbsp;↓|Aplicativo<br>Serviço<br>Java SE|Aplicativo<br>Serviço<br>Tomcat|Azure<br>Spring<br>Nuvem|AKS|Máquinas Virtuais|
|---|---|---|---|---|---|---|
| WildFly/<br>JBoss AS | N/D | N/D | N/D | [diretrizes][9] | diretrizes<br>planejado |
| WebLogic              | N/D | N/D | N/D | [diretrizes][6] | [diretrizes][4]       |
| WebSphere             | N/D | N/D | N/D | [diretrizes][7] | diretrizes<br>planejado |
| JBoss EAP             | N/D | N/D | N/D | [diretrizes][8] | diretrizes<br>planejado |

<!-- reference links, for use with tables -->
[1]: media/migration-overview/logo_azure.svg
[2]: migrate-tomcat-to-tomcat-app-service.md
[3]: migrate-tomcat-to-containers-on-azure-kubernetes-service.md
[4]: migrate-weblogic-to-virtual-machines.md
[5]: migrate-java-se-to-java-se-app-service.md
[6]: migrate-weblogic-to-wildfly-on-azure-kubernetes-service.md
[7]: migrate-websphere-to-wildfly-on-azure-kubernetes-service.md
[8]: migrate-jboss-eap-to-wildfly-on-azure-kubernetes-service.md
[9]: migrate-wildfly-to-wildfly-on-azure-kubernetes-service.md
