---
title: Implantar um serviço MicroProfile em Java
titleSuffix: Azure Web App for Containers
description: Saiba como implantar um serviço MicroProfile usando o Docker e o Aplicativo Web para Contêineres do Azure
services: container-registry;app-service
documentationcenter: java
author: jonathangiles
ms.author: jogiles
ms.date: 09/07/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: d24300d02df1fdd079c7ba3c5f2ef3370db3c5e8
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81670572"
---
# <a name="deploy-a-java-based-microprofile-service-to-azure-web-app-for-containers"></a>Implantar um serviço MicroProfile baseado em Java para o Aplicativo Web para Contêineres do Azure

MicroProfile é uma ótima maneira para compilar aplicativos Java extremamente pequenos que podem ser rápida e facilmente implantados nos serviços, como [Aplicativo Web para Contêineres do Azure](https://azure.microsoft.com/services/app-service/containers/). Neste tutorial, criaremos um microsserviço baseado em MicroProfile que é colocado em um contêiner do Docker, depois implantado em um [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/) e, por fim, hospedado usando o Aplicativo Web para Contêineres do Azure.

> [!NOTE]
> Este procedimento funciona com qualquer implementação de MicroProfile.io, desde que a imagem de contêiner do Docker seja executada automaticamente (ou seja, inclui o runtime).

Mais concretamente, este exemplo usa o [Payara Micro](https://www.payara.fish/payara_micro) e o [MicroProfile 1.3](https://microprofile.io/) para criar um arquivo .war Java pequeno (5.085 bytes no computador do autor) e empacotá-lo em uma imagem do Docker (que é de aproximadamente 174 megabytes). Essa imagem do Docker contém tudo que é necessário para uma implantação totalmente em contêiner desse aplicativo Web.

Em geral, devido ao modo como o Docker funciona, não é necessário reimplantar a imagem do Docker de 174 megabytes inteira sempre que o código-fonte do aplicativo é alterado, já que o Docker carregará somente as diferenças. Portanto, o processo de execução de uma nova versão de um aplicativo MicroProfile por meio de um pipeline de CI/CD é eficiente e rápido, reduzindo fricção e habilitando rápida iteração de desenvolvimento.

Neste tutorial, primeiro criaremos e executaremos o código localmente e depois o implantaremos como um aplicativo Web no Azure. Em ambos os casos, dependeremos do Docker para simplificar e padronizar nossos esforços. Antes de começar, vamos criar um Registro de Contêiner do Azure para armazenar nossos contêineres do Docker.

## <a name="creating-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure

Usaremos o [portal do Azure](https://portal.azure.com) para criar o Registro de Contêiner do Azure, mas observe que há opções alternativas, como a CLI do Azure. Siga as etapas abaixo para criar um novo Registro de Contêiner do Azure:

1. Faça logon no [portal do Azure](https://portal.azure.com) e crie um recurso do Registro de Contêiner do Azure. Forneça um nome de registro (observe que esse nome é o que deveria ser definido como a propriedade `docker.registry` em *pom.xml*). Altere os padrões como desejar e clique em **Criar**.

1. Quando o registro de contêiner estiver ativo (o que leva cerca de 30 segundos depois de clicar em **Criar**), clique nele e, em seguida, no link **Chaves de acesso** na área do menu à esquerda. Aqui, você precisa habilitar não só a configuração **usuário administrador** para que esse registro de contêiner possa ser acessado de nossos computadores (a fim de enviar por push contêineres do Docker para ele), mas também o acesso da instância dos Aplicativos Web para Contêineres do Azure que em breve configuraremos.

1. Enquanto estiver na área **Chave de acesso**, observe os valores `username` e `password`. Copiaremos e colaremos esses valores em nosso arquivo global do Maven *settings.xml* (para obter mais informações sobre as configurações do Maven, confira o site [Apache Maven Project](https://maven.apache.org/settings.html)). Para referência, aqui está uma versão oculta do arquivo *${user.home}/.m2/settings.xml* no sistema do autor:

    ```xml
    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                          https://maven.apache.org/xsd/settings-1.0.0.xsd">
        <servers>
          <server>
            <id>jogilescr.azurecr.io</id>
            <username>jogilescr</username>
            <password>ojoirshois.this-isn't-real.hrihslirhlishrglih</password>
          </server>
        </servers>
    </settings>
    ```

Agora, podemos prosseguir com a criação e a execução de nosso aplicativo MicroProfile localmente.

## <a name="creating-our-microprofile-application"></a>Criar nosso aplicativo MicroProfile

Este exemplo é baseado em um aplicativo de exemplo disponível no GitHub, para que possamos clonar e, em seguida, percorrer o código. Siga as etapas abaixo para obter o código clonado em seu computador:

1. `git clone https://github.com/Azure-Samples/microprofile-docker-helloworld.git`

1. `cd microprofile-docker-helloworld`

Nesse diretório, há um arquivo *pom.xml* que é usado para especificar o projeto no formato usado pela ferramenta de criação do Maven. Esse arquivo pode ser editado para atender às suas necessidades. Em particular, as propriedades `docker.registry` e `docker.name` deveriam ser alteradas para as `docker.registry` e `docker.name` criadas quando o Registro de Contêiner do Azure foi configurado.

Outro arquivo importante neste diretório é o Dockerfile, que é reproduzido abaixo:

```dockerfile
FROM payara/micro

ARG WAR_FILE
COPY target/${WAR_FILE} $DEPLOY_DIR

EXPOSE 8080
```

Esse Dockerfile simplesmente cria um contêiner do Docker baseado no contêiner do Docker do Payara Micro e copia o arquivo *.war* que é criado como parte de nosso processo de criação. Ele também expõe a porta 8080 para que possamos acessar o serviço depois que ele está em execução dentro de um contêiner do Docker.

Aprofundando-nos no diretório *src*, descobriremos a classe `Application` reproduzida abaixo:

```java
package com.microsoft.azure.samples.microprofile.docker.helloworld;

import javax.ws.rs.ApplicationPath;

@ApplicationPath("/api")
public class Application extends javax.ws.rs.core.Application { }
```

A anotação `@ApplicationPath("/api")` especifica o ponto de extremidade base para esse microsserviço, ou seja, que todos os pontos de extremidade terão `/api` antes do restante da URL necessária para acessar qualquer ponto de extremidade REST específico.

Dentro do pacote `api` está uma classe chamada `API`, que contém o código a seguir:

```java
package com.microsoft.azure.samples.microprofile.docker.helloworld.api;

import javax.enterprise.context.ApplicationScoped;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;

import static javax.ws.rs.core.MediaType.TEXT_HTML;

@ApplicationScoped
@Path("/")
public class API {

    @GET
    @Path("/helloworld")
    @Produces(TEXT_HTML)
    public String info() {
        return "Hello, world!";
    }
}
```

Com o uso da anotação `@Path("/helloworld")`, podemos ver que esse ponto de extremidade REST, quando combinado com o `/api` especificado na classe `Application`, será `/api/helloworld`. Quando esse ponto de extremidade é chamado usando uma solicitação HTTP GET, podemos ver que o método produzirá texto/html e, na verdade, é simplesmente uma cadeia de caracteres embutidos em código “Olá, mundo!”.

Agora, abordamos todo o código necessário para criar um microsserviço usando MicroProfile. Agora podemos usar Maven para compilá-lo, colocar no contêiner do Docker e executá-lo localmente. Você pode fazer isso através das seguintes etapas:

1. Executar `mvn clean package` e aguardar até que ele seja concluído com êxito.

1. Executar `docker run -it --rm -p 8080:8080 <docker.registry>/<docker.name>:latest`, por exemplo, `docker run -it --rm -p 8080:8080 jogilescr.azurecr.io/samples/docker-helloworld:latest`, se seu `docker.registry` é `jogilescr.azurecr.io` e `docker.name` é `samples/docker-helloworld`.

1. Tentar acessar [http://localhost:8080/microprofile/api/helloworld](http://localhost:8080/microprofile/api/helloworld) e [http://localhost:8080/health](http://localhost:8080/health) no navegador da Web. Se você vir a resposta esperada “Olá, mundo!” (e as informações relacionadas à integridade para o ponto de extremidade [/integridade](http://localhost:8080/health)), você implantou com êxito o aplicativo MicroProfile em seu computador local.

## <a name="pushing-to-the-azure-container-registry"></a>Enviar por push o Registro de Contêiner do Azure

Agora que criamos e executamos com êxito o aplicativo MicroProfile em nosso computador local, a próxima etapa é enviar por push esse contêiner para o registro de contêiner. Neste tutorial, estamos usando o Registro de Contêiner do Azure, mas qualquer registro de contêiner funcionará (desde que o arquivo `pom.xml` seja editado a fim de apontar para a localização relevante).

1. Execute `mvn clean package` para limpar, compilar e criar uma imagem do Docker local.

2. Execute `mvn dockerfile:push` para enviar por push ao Registro de Contêiner do Azure.

Neste estágio você tem sua imagem de contêiner do Docker carregada no Registro de Contêiner do Azure, mas ele ainda não está em execução, pois agora temos que implantá-lo em uma instância do Aplicativo Web para Contêineres do Azure. Agora faremos isso.

## <a name="creating-an-azure-web-app-for-containers-instance"></a>Criar uma instância de Aplicativo Web para Contêineres do Azure

1. Retorne ao [portal do Azure](https://portal.azure.com) e crie uma instância do Aplicativo Web para Contêineres (localizado no título 'Web + Celular' no menu). Algumas dicas:

   1. O nome que você especificar aqui será a URL pública do aplicativo Web (embora um domínio personalizado possa ser adicionado mais tarde, se desejado). Portanto, é uma boa ideia escolher um nome que você possa se lembrar com facilidade.

   1. Quando chegar à seção “Configurar contêiner”, você pode selecionar “Registro de Contêiner do Azure” para a “Origem da imagem” e, em seguida, selecionar a imagem correta das listas suspensas.

   1. Não é necessário especificar nenhum valor no campo “Arquivo de inicialização”.

1. Depois que a instância for criada (novamente, isso é muito rápido), clique nela e, em seguida, clique no item de menu de “Configurações do aplicativo”. Aqui você precisa adicionar uma nova configuração de aplicativo, onde a chave é `WEBSITES_PORT` e o valor é `8080`. Isso informa ao Azure a porta que você deseja expor no contêiner e será mapeada para a porta 80 externamente.

1. Opcionalmente, clique no link “Contêiner do Docker” e habilite a “Implantação contínua”, de modo que sempre que você atualizar a imagem do Registro de Contêiner do Azure ela é atualizada automaticamente na instância do Aplicativo Web para Contêineres do Azure.

1. Você deve ser capaz de acessar as instâncias hospedadas no Azure em `http://<appname>.azurewebsites.net/microprofile/api/helloworld` e `http://<appname>.azurewebsites.net/health`.

## <a name="summary"></a>Resumo

Neste tutorial percorremos o processo de criação de um microsserviço baseado em MicroProfile simples, em um contêiner Docker, e o executamos localmente e publicamos no Azure. Estender o nosso microsserviço para fornecer funcionalidades mais úteis está fora do escopo deste tutorial, mas há uma grande quantidade de tutoriais e conselhos sobre MicroProfile na Internet e os leitores são incentivados a analisar [MicroProfile.io](https://microprofile.io/) para obter mais conteúdo.
