---
author: yevster
ms.author: yebronsh
ms.date: 8/25/2020
ms.openlocfilehash: 6b2ad5c8490cd4b1c450426f8e7d728cd39eaea3
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062038"
---
Agora que você concluiu a migração, confirme se o aplicativo funciona conforme o esperado. Em seguida, você pode tornar seu aplicativo mais nativo de nuvem usando as recomendações a seguir.

* Considere a possibilidade de habilitar seu aplicativo para que ele funcione com o Registro do Spring Cloud. Isso permitirá que o aplicativo seja descoberto dinamicamente por outros microsserviços e clientes implantados. Para saber mais, confira [Tutorial: Preparar um aplicativo Spring Java para implantação](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment). Em seguida, modifique todos os clientes do aplicativo para que eles usem o balanceador de carga do Cliente Spring. Isso permite que o cliente obtenha endereços de todas as instâncias do aplicativo em execução e localize uma instância que funcionará se outra instância for corrompida ou ficar sem resposta. Para obter mais informações, confira [Dicas do Spring: Load Balancer do Spring Cloud](https://spring.io/blog/2020/03/25/spring-tips-spring-cloud-loadbalancer) no Spring Blog.

* Em vez de tornar seu aplicativo público, considere a possibilidade de adicionar uma instância do [Spring Cloud Gateway](https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/current/reference/html/). O Spring Cloud Gateway fornece um só ponto de extremidade para todos os aplicativos/microsserviços implantados na sua instância do Azure Spring Cloud. Se um Spring Cloud Gateway já estiver implantado, verifique se ele está configurado para rotear o tráfego para o aplicativo recém-implantado.

* Considere a possibilidade de adicionar um Config Server do Spring Cloud para gerenciar de maneira centralizada a configuração e fazer o controle de versão dela para todos os microsserviços do Spring Cloud. Primeiro, crie um repositório Git para hospedar a configuração e configure a instância do Azure Spring Cloud para usá-la. Para saber mais, confira [Tutorial: Configurar uma instância do Servidor de Configuração do Spring Cloud para seu serviço](/azure/spring-cloud/spring-cloud-tutorial-config-server). Em seguida, migre sua configuração usando as seguintes etapas:

  1. Dentro do diretório *src/main/resources* do aplicativo, crie um arquivo *bootstrap.yml* com o seguinte conteúdo:

        ```yml
          spring:
            application:
              name: <your-application-name>
        ```

  1. No repositório do Git de configuração, crie um arquivo *<nome-do-aplicativo>.yml*, em que `your-application-name` é o mesmo que na etapa anterior. Mova as configurações do arquivo *application.yml* em *src/main/resources* para o novo arquivo que você acabou de criar. Se as configurações estavam antes em um arquivo *.properties*, elas precisam ser convertidas para YAML primeiro. Você pode encontrar ferramentas online ou plug-ins IntelliJ para realizar essa conversão.

  1. Crie um arquivo *application.yml* no diretório acima. Você pode usar esse arquivo para definir as configurações e os recursos que serão compartilhados entre todos os aplicativos na instância do Azure Spring Cloud. Essas configurações normalmente incluem fontes de dados, configurações de registro em log, configuração do acionador do Spring Boot, entre outros.

  1. Faça commit dessas alterações e efetue push delas para o repositório Git.

  1. Remova o arquivo *application.properties* ou *application.yml* do aplicativo.

* Considere adicionar um pipeline de implantação para implantações automáticas e consistentes. As instruções estão disponíveis [Para o Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), [Para o GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) e [Para o Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service).

* Considere usar implantações de preparo para testar alterações de código em produção antes que elas sejam disponibilizadas para alguns ou todos os usuários finais. Para obter mais informações, confira [Configurar um ambiente de preparo no Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-staging-environment).

* Considere adicionar associações de serviço para conectar o aplicativo a bancos de dados do Azure com suporte. Essas associações de serviço eliminariam a necessidade de fornecer informações de conexão, incluindo credenciais, para os aplicativos Spring Cloud.

* Considere a possibilidade de usar o rastreamento distribuído e o Azure App Insights para monitorar o desempenho e as interações dos seus aplicativos. Para obter mais informações, confira [Usar o rastreamento distribuído com o Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing).

* Considere adicionar as regras de alerta e os grupos de ações do Azure Monitor para detectar e abordar as condições anormais rapidamente. Para saber mais, confira [Tutorial: Monitorar recursos do Spring Cloud usando alertas e grupos de ações](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups).

* Considere replicar a implantação do Azure Spring Cloud em outra região para obter latência mais baixa, maior confiabilidade e tolerância a falhas. Use o [Gerenciador de Tráfego do Azure](/azure/traffic-manager) para balancear a carga entre implantações ou o [Azure Front Door](/azure/frontdoor) para adicionar o descarregamento de SSL e o firewall do aplicativo Web com proteção contra DDoS.

* Se a replicação geográfica não for necessária, considere adicionar um [Gateway de Aplicativo do Azure](/azure/application-gateway) para adicionar o descarregamento de SSL e o Firewall do aplicativo Web com proteção contra DDoS.
