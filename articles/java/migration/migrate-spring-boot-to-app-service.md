---
title: Migrar aplicativos Spring Boot para o Serviço de Aplicativo do Azure
description: Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Spring Boot existente para o Serviço de Aplicativo do Azure.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 01/22/2019
ms.custom: devx-track-java
ms.openlocfilehash: f5edea5267bd79db7de890b6030db3e5d02f0ea2
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379760"
---
# <a name="migrate-spring-boot-applications-to-azure-app-service"></a>Migrar aplicativos Spring Boot para o Serviço de Aplicativo do Azure

Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Spring Boot existente para o Serviço de Aplicativo do Azure.

## <a name="pre-migration"></a>Pré-migração

Antes de tudo, para garantir uma migração bem-sucedida, conclua as etapas de avaliação e de inventário descritas nas seções a seguir.

Se você não puder atender a nenhum desses requisitos de pré-migração, confira os seguintes guias de migração complementares:

* Migrar aplicativos JAR executáveis para contêineres no Serviço de Kubernetes do Azure (diretrizes planejadas)
* Migrar aplicativos JAR executáveis para Máquinas Virtuais do Azure (diretrizes planejadas)

### <a name="switch-to-a-supported-platform"></a>Alternar para uma plataforma compatível

O Serviço de Aplicativo oferece versões específicas do Java SE. Para garantir a compatibilidade, migre seu aplicativo para uma das versões compatíveis do ambiente atual antes de continuar com qualquer uma das etapas restantes. É necessário testar completamente a configuração resultante. Use a versão estável mais recente da sua distribuição do Linux nesses testes.

[!INCLUDE [note-obtain-your-current-java-version-app-service](includes/note-obtain-your-current-java-version-app-service.md)]

### <a name="inventory-external-resources"></a>Recursos externos de inventário

Identifique recursos externos, como fontes de dados, agentes de mensagens JMS e URLs de outros serviços. Em aplicativos Spring Boot, normalmente é possível encontrar a configuração desses recursos na pasta *src/main/directory*, em um arquivo geralmente chamado de *application.properties* ou de *application.yml*. Além disso, verifique as variáveis de ambiente da implantação de produção para ver as definições de configuração pertinentes.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

Depois de identificar os agentes em uso, localize as configurações correspondentes. Em aplicativos Spring Boot, normalmente é possível encontrá-las nos arquivos *application.properties* e *application.yml* no diretório de aplicativo.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

[!INCLUDE [inventory-identity-providers-spring-boot](includes/inventory-identity-providers-spring-boot.md)]

#### <a name="all-other-external-resources"></a>Todos os outros recursos externos

Não é possível documentar todas as dependências externas possíveis neste guia. É responsabilidade da sua equipe verificar se todas as dependências externas de seu aplicativo podem ser atendidas após uma migração do Serviço de Aplicativo.

### <a name="inventory-secrets"></a>Segredos de inventário

#### <a name="passwords-and-secure-strings"></a>Senhas e cadeias de caracteres seguras

Verifique todas as propriedades e os arquivos de configuração, assim como as variáveis de ambiente, nas implantações de produção em busca de senhas e cadeias de caracteres secretas. Em um aplicativo Spring Boot, essas cadeias de caracteres provavelmente serão encontradas em *application.properties* ou *application.yml*.

[!INCLUDE [inventory-certificates-h4](includes/inventory-certificates-h4.md)]

[!INCLUDE [determine-whether-and-how-the-file-system-is-used](includes/determine-whether-and-how-the-file-system-is-used.md)]

### <a name="special-cases"></a>Casos especiais

Determinados cenários de produção podem exigir alterações adicionais ou impor limitações adicionais. Embora esses cenários sejam raros, é importante verificar se eles não se aplicam ao aplicativo ou estão resolvidos corretamente.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Determinar se o aplicativo depende de trabalhos agendados

Trabalhos agendados, como tarefas do Agendador do Quartz ou trabalhos cron, não podem ser usados com o Serviço de Aplicativo. O Serviço de Aplicativo não impedirá que você implante um aplicativo que contém tarefas agendadas internamente. No entanto, se o aplicativo for escalado horizontalmente, um mesmo trabalho agendado poderá ser executado mais de uma vez por período agendado. Essa situação pode levar a consequências indesejadas.

Faça um inventário de todos os trabalhos agendados, dentro ou fora do processo de aplicativos.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Determinar se o aplicativo contém código específico do sistema operacional

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

#### <a name="identify-all-outside-processesdaemons-running-on-the-production-servers"></a>Identificar todos os processos/daemons externos em execução nos servidores de produção

Os processos em execução fora do Servidor de Aplicativos, como o monitoramento de daemons, precisarão ser migrados para outro lugar ou eliminados.

#### <a name="identify-handling-of-non-http-requests-or-multiple-ports"></a>Identificar o tratamento de solicitações não HTTP ou várias portas

O Serviço de Aplicativo dá suporte a apenas um único ponto de extremidade HTTP em uma única porta. Se o aplicativo escutar várias portas ou aceitar solicitações usando protocolos diferentes do HTTP, não use o Serviço de Aplicativo do Azure.

## <a name="migration"></a>Migração

### <a name="parameterize-the-configuration"></a>Parametrizar a configuração

Verifique se todas as coordenadas de recursos externos (como cadeias de conexão de banco de dados) e outras configurações personalizáveis podem ser lidas nas variáveis de ambiente. Se você estiver migrando um aplicativo Spring Boot, todas as definições de configuração já deverão ser externalizáveis. Para obter mais informações, consulte [Configuração externalizada](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config) na documentação do Spring Boot.

Este é um exemplo que faz referência a uma variável de ambiente `SERVICEBUS_CONNECTION_STRING` de um arquivo *application.properties*:

```properties
spring.jms.servicebus.connection-string=${SERVICEBUS_CONNECTION_STRING}
spring.jms.servicebus.topic-client-id=contoso1
spring.jms.servicebus.idle-timeout=10000
```

### <a name="provision-an-app-service-plan"></a>Provisionar um plano do Serviço de Aplicativo

Na [lista de planos de serviço disponíveis](https://azure.microsoft.com/pricing/details/app-service/linux/), selecione o plano cujas especificações correspondem às do hardware de produção atual ou as superam.

> [!NOTE]
> Se você planeja executar implantações de preparo/canário ou usar [slots de implantação](/azure/app-service/deploy-staging-slots), o plano do Serviço de Aplicativo precisará incluir essa capacidade adicional. É recomendável usar planos Premium ou superiores para aplicativos Java.

[Crie o plano do Serviço de Aplicativo](/azure/app-service/app-service-plan-manage#create-an-app-service-plan).

### <a name="create-and-deploy-web-apps"></a>Criar e implantar aplicativos Web

Você precisará criar um aplicativo Web em seu Plano do Serviço de Aplicativo (escolhendo "Java SE" como a pilha de tempo de execução) para cada arquivo JAR executável que pretende executar.

#### <a name="maven-applications"></a>Aplicativos Maven

Se o seu aplicativo for criado com base em um arquivo POM do Maven, [use o plug-in do Aplicativo Web para Maven](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-with-maven-plugin#configure-maven-plugin-for-azure-app-service) para criar o aplicativo Web e implantar seu aplicativo.

#### <a name="non-maven-applications"></a>Aplicativos não Maven

Se não for possível usar o plug-in do Maven, você precisará provisionar o aplicativo Web por meio de outros mecanismos, como:

* [Azure portal](https://portal.azure.com/#create/Microsoft.WebSite)
* [CLI do Azure](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)
* [PowerShell do Azure](/powershell/module/az.websites/new-azwebapp)

Depois que o aplicativo Web tiver sido criado, use um dos [mecanismos de implantação disponíveis](/azure/app-service/deploy-ftp) para implantar o aplicativo. Se possível, seu aplicativo deverá ser carregado em */home/site/wwwroot/app.jar*. Se não desejar renomear o JAR para *app.jar*, você poderá carregar um script de shell com o comando para executar o JAR. Em seguida, cole o caminho completo desse script na caixa de texto [Arquivo de inicialização](/azure/app-service/containers/app-service-linux-faq#built-in-images), na seção Configuração do portal. O script de inicialização não é executado por meio do diretório no qual ele é colocado. Portanto, sempre use caminhos absolutos para fazer referência a arquivos em seu script de inicialização (por exemplo: `java -jar /home/myapp/myapp.jar`).

### <a name="migrate-jvm-runtime-options"></a>Migrar opções de runtime da JVM

Se o aplicativo exigir opções específicas de runtime, [use o mecanismo mais apropriado para especificá-las](/azure/app-service/containers/configure-language-java#set-java-runtime-options).

[!INCLUDE [configure-custom-domain-and-ssl](includes/configure-custom-domain-and-ssl.md)]

[!INCLUDE [import-backend-certificates](includes/import-backend-certificates.md)]

### <a name="migrate-external-resource-coordinates-and-other-settings"></a>Migrar coordenadas de recursos externos e outras configurações

Siga [estas etapas para migrar cadeias de conexão e outras configurações](/azure/app-service/containers/configure-language-java#spring-boot-1).

> [!NOTE]
> Para todas as configurações do aplicativo Spring Boot parametrizadas com variáveis na seção [Parametrizar a configuração](#parameterize-the-configuration), essas variáveis de ambiente devem ser definidas na configuração de aplicativo. Todas as configurações de aplicativo do Spring Boot não explicitamente parametrizadas com variáveis de ambiente ainda podem ser substituídas por elas por meio da Configuração de aplicativo. Por exemplo:

  ```properties
  spring.jms.servicebus.connection-string=${CUSTOMCONNSTR_SERVICE_BUS}
  spring.jms.servicebus.topic-client-id=contoso1
  spring.jms.servicebus.idle-timeout=1800000
  ```

![Configuração de aplicativo do Serviço de Aplicativo](media/migrate-spring-boot-to-app-service/app-service-parameterized-spring-boot-app-settings.png)

[!INCLUDE [migrate-scheduled-jobs](includes/migrate-scheduled-jobs.md)]

[!INCLUDE [migrate-identity-provider-app-service.md](includes/migrate-identity-provider-app-service.md)]

### <a name="restart-and-smoke-test"></a>Reinicialização e smoke test

Por fim, você precisará reiniciar seu aplicativo Web para aplicar todas as alterações de configuração. Após a conclusão da reinicialização, verifique se o aplicativo está sendo executado corretamente.

## <a name="post-migration"></a>Após a migração

Agora que você migrou seu aplicativo para o Serviço de Aplicativo do Azure, você deve verificar se ele funciona conforme o esperado. Depois de fazer isso, temos algumas recomendações para você que podem tornar seu aplicativo mais nativo da nuvem.

### <a name="recommendations"></a>Recomendações

* Se você optou por usar o diretório */home* para armazenamento de arquivos, considere [substituí-lo pelo Armazenamento do Azure](/azure/app-service/containers/how-to-serve-content-from-azure-storage).

* Se você tiver uma configuração no diretório */home* que contenha cadeias de conexão, chaves SSL e outras informações secretas, considere usar [Azure Key Vault](/azure/app-service/app-service-key-vault-references) e/ou [injeção de parâmetro com configurações de aplicativo](/azure/app-service/configure-common#configure-app-settings) sempre que possível.

* Considere [usar slots de implantação](/azure/app-service/deploy-staging-slots) para implantações confiáveis sem nenhum tempo de inatividade.

* Crie e implemente uma estratégia de DevOps. Para manter a confiabilidade, aumentando simultaneamente a velocidade de desenvolvimento, considere [automatizar implantações e testar com Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp). Ao usar slots de implantação, você pode [automatizar a implantação em um slot](/azure/devops/pipelines/targets/webapp?view=azure-devops&tabs=yaml#deploy-to-a-slot) e depois trocá-lo por outro.

* Criar a implementar uma estratégia de continuidade dos negócios e recuperação de desastre. Para aplicativos críticos, considere a possibilidade de [usar uma arquitetura de implantação em várias regiões](/azure/architecture/reference-architectures/app-service-web-app/multi-region).
