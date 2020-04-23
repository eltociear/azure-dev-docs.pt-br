---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 6d6f0f701e9507f5dee065c14b48b30be423e100
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81672842"
---
### <a name="create-a-docker-image-for-wildfly"></a>Criar uma imagem de Docker para o WildFly

Para criar um Dockerfile, você precisará dos seguintes pré-requisitos:

* Um JDK com suporte
* Uma instalação do WildFly
* Suas opções de runtime do JVM.
* Uma maneira de passar variáveis de ambiente (se aplicável).

Em seguida, você pode executar as etapas descritas nas seções a seguir, quando aplicável. Você pode usar o [Repositório de Início Rápido do Contêiner WildFly](https://github.com/Azure/wildfly-container-quickstart) como um ponto de partida para seu Dockerfile e aplicativo Web.

1. [Configurar FlexVolume do KeyVault](#configure-keyvault-flexvolume)
2. [Configurar as fontes de dados](#set-up-data-sources)
3. [Configurar os recursos de JNDI](#set-up-jndi-resources)
4. [Examinar a configuração do WildFly](#review-wildfly-configuration)

#### <a name="configure-keyvault-flexvolume"></a>Configurar FlexVolume do KeyVault

Crie um Azure KeyVault e preencha todos os segredos necessários. Para saber mais, confira [Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure](/azure/key-vault/quick-create-cli). Em seguida, configure um [FlexVolume do KeyVault](https://github.com/Azure/kubernetes-keyvault-flexvol/blob/master/README.md) para tornar esses segredos acessíveis para pods.

Também será necessário atualizar o script de inicialização usado para inicializar o WildFly. Esse script deve importar os certificados para o keystore usado pelo WildFly antes de iniciar o servidor.

#### <a name="set-up-data-sources"></a>Principais fontes de dados

Para configurar o WildFly para acessar uma fonte de dados, você precisará adicionar o JAR do driver JDBC à imagem do Docker e, em seguida, executar os comandos apropriados da CLI do JBoss. Esses comandos devem configurar a fonte de dados ao criar a imagem do Docker.

As etapas a seguir fornecem instruções para PostgreSQL, MySQL e SQL Server.

1. Baixe o driver JDBC para [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/) ou [SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

    Descompacte o arquivo baixado para obter o arquivo .jar do driver.

1. Crie um arquivo com um nome como `module.xml` e adicione a marcação a seguir. Substitua o espaço reservado `<module name>` (incluindo os colchetes angulares) por `org.postgres` para PostgreSQL, `com.mysql` para MySQL ou `com.microsoft` para SQL Server. Substitua `<JDBC .jar file path>` pelo nome do arquivo .jar da etapa anterior, incluindo o caminho completo para o local em que você colocará o arquivo na imagem do Docker, por exemplo, em `/opt/database`.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Crie um arquivo com um nome como `datasource-commands.cli` e adicione o código a seguir. Substitua `<JDBC .jar file path>` pelo valor usado na etapa anterior. Substitua `<module file path>` pelo nome do arquivo e pelo caminho da etapa anterior, por exemplo, `/opt/database/module.xml`.

    **PostgreSQL**

    ```console
    batch
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)
    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    reload
    run batch
    shutdown
    ```

    **MySQL**

    ```console
    batch
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)
    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter
    reload
    run batch
    shutdown
    ```

    **SQL Server**

    ```console
    batch
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)
    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter
    reload
    run batch
    shutdown
    ```

1. Atualize a configuração da fonte de dados de JTA para seu aplicativo:

    Abra o arquivo `src/main/resources/META-INF/persistence.xml` para o aplicativo e localize o elemento `<jta-data-source>`. Substitua o conteúdo como mostrado aqui:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

1. Adicione o seguinte ao seu `Dockerfile` para que a fonte de dados seja criada quando você criar a imagem do Docker

    ```console
    RUN /bin/bash -c '<WILDFLY_INSTALL_PATH>/bin/standalone.sh --start-mode admin-only &' && \
    sleep 30 && \
    <WILDFLY_INSTALL_PATH>/bin/jboss-cli.sh -c --file=/opt/database/datasource-commands.cli && \
    sleep 30
    ```

1. Determine o `DATABASE_CONNECTION_URL` a ser usado, pois eles são diferentes para cada servidor de banco de dados e diferentes dos valores no portal do Azure. Os formatos de URL mostrados aqui são necessários para o uso do WildFly:

    **PostgreSQL**

    ```console
    jdbc:postgresql://<database server name>:5432/<database name>?ssl=true
    ```

    **MySQL**

    ```console
    jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT
    ```

    **SQL Server**

    ```console
    jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

1. Ao criar seu YAML de implantação em um estágio posterior, você precisará passar as seguintes variáveis de ambiente, `DATABASE_CONNECTION_URL`, `DATABASE_SERVER_ADMIN_FULL_NAME` e `DATABASE_SERVER_ADMIN_PASSWORD`, com os valores apropriados.

Para obter mais informações sobre como configurar a conectividade do banco de dados com o WildFly, confira [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) ou [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

#### <a name="set-up-jndi-resources"></a>Configurar os recursos de JNDI

Para configurar cada recurso de JNDI que é necessário configurar no WildFly, você geralmente usará as seguintes etapas:

1. Baixe os arquivos JAR necessários e copie-os para a imagem do Docker.
2. Crie um arquivo *module.xml* do WildFly que faz referência a esses arquivos JAR.
3. Crie qualquer configuração necessária para o recurso JNDI específico.
4. Crie um script da CLI do JBoss a ser usado durante a compilação do Docker para registrar o recurso de JNDI.
5. Adicione tudo ao Dockerfile.
6. Passe as variáveis de ambiente apropriadas em seu YAML de implantação.

O exemplo a seguir mostra as etapas necessárias para criar o recurso de JNDI para conectividade do JMS com o Barramento de Serviço do Azure.

1. Baixe o [provedor de JMS Apache Qpid](https://qpid.apache.org/components/jms/index.html)

    Descompacte o arquivo baixado para obter os arquivos .jar.

1. Crie um arquivo com um nome como `module.xml` e adicione a marcação a seguir em `/opt/servicebus`. Verifique se os números de versão dos arquivos JAR estão alinhados com os nomes dos arquivos JAR da etapa anterior.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.jboss.genericjms.provider">
     <resources>
      <resource-root path="proton-j-0.31.0.jar"/>
      <resource-root path="qpid-jms-client-0.40.0.jar"/>
      <resource-root path="slf4j-log4j12-1.7.25.jar"/>
      <resource-root path="slf4j-api-1.7.25.jar"/>
      <resource-root path="log4j-1.2.17.jar"/>
      <resource-root path="netty-buffer-4.1.32.Final.jar" />
      <resource-root path="netty-codec-4.1.32.Final.jar" />
      <resource-root path="netty-codec-http-4.1.32.Final.jar" />
      <resource-root path="netty-common-4.1.32.Final.jar" />
      <resource-root path="netty-handler-4.1.32.Final.jar" />
      <resource-root path="netty-resolver-4.1.32.Final.jar" />
      <resource-root path="netty-transport-4.1.32.Final.jar" />
      <resource-root path="netty-transport-native-epoll-4.1.32.Final-linux-x86_64.jar" />
      <resource-root path="netty-transport-native-kqueue-4.1.32.Final-osx-x86_64.jar" />
      <resource-root path="netty-transport-native-unix-common-4.1.32.Final.jar" />
      <resource-root path="qpid-jms-discovery-0.40.0.jar" />
     </resources>
     <dependencies>
      <module name="javax.api"/>
      <module name="javax.jms.api"/>
     </dependencies>
    </module>
    ```

1. Crie um arquivo `jndi.properties` em `/opt/servicebus`.

    ```console
    connectionfactory.${MDB_CONNECTION_FACTORY}=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=${SB_SAS_POLICY}&jms.password=${SB_SAS_KEY}
    queue.${MDB_QUEUE}=${SB_QUEUE}
    topic.${MDB_TOPIC}=${SB_TOPIC}
    ```

1. Crie um arquivo com um nome como `servicebus-commands.cli` e adicione o código a seguir.

    ```console
    batch
    /subsystem=ee:write-attribute(name=annotation-property-replacement,value=true)
    /system-property=property.mymdb.queue:add(value=myqueue)
    /system-property=property.connection.factory:add(value=java:global/remoteJMS/SBF)
    /subsystem=ee:list-add(name=global-modules, value={"name" => "org.jboss.genericjms.provider", "slot" =>"main"}
    /subsystem=naming/binding="java:global/remoteJMS":add(binding-type=external-context,module=org.jboss.genericjms.provider,class=javax.naming.InitialContext,environment=[java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory,org.jboss.as.naming.lookup.by.string=true,java.naming.provider.url=/opt/servicebus/jndi.properties])
    /subsystem=resource-adapters/resource-adapter=generic-ra:add(module=org.jboss.genericjms,transaction-support=XATransaction)
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:add(class-name=org.jboss.resource.adapter.jms.JmsManagedConnectionFactory, jndi-name=java:/jms/${MDB_CONNECTION_FACTORY})
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=ConnectionFactory:add(value=${MDB_CONNECTION_FACTORY})
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=JndiParameters:add(value="java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory;java.naming.provider.url=/opt/servicebus/jndi.properties")
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:write-attribute(name=security-application,value=true)
    /subsystem=ejb3:write-attribute(name=default-resource-adapter-name, value=generic-ra)
    run-batch
    reload
    shutdown
    ```

1. Adicione o seguinte ao seu `Dockerfile` para que o recurso de JNDI seja criado quando você criar a imagem do Docker

    ```console
    RUN /bin/bash -c '<WILDFLY_INSTALL_PATH>/bin/standalone.sh --start-mode admin-only &' && \
    sleep 30 && \
    <WILDFLY_INSTALL_PATH>/bin/jboss-cli.sh -c --file=/opt/servicebus/servicebus-commands.cli && \
    sleep 30
    ```

1. Ao criar seu YAML de implantação em um estágio posterior, você precisará passar as seguintes variáveis de ambiente, `MDB_CONNECTION_FACTORY`, `DEFAULT_SBNAMESPACE` e `SB_SAS_POLICY`, `SB_SAS_KEY`, `MDB_QUEUE`, `SB_QUEUE`, `MDB_TOPIC` e `SB_TOPIC` com os valores apropriados.

#### <a name="review-wildfly-configuration"></a>Examinar a configuração do WildFly

Examine o [Guia do Administrador do WildFly](https://docs.wildfly.org/18/Admin_Guide.html) para consultar etapas pré-migração adicionais não abordadas pelas orientações anteriores.
