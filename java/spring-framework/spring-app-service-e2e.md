---
title: Implantar um aplicativo Spring/Tomcat no Serviço de Aplicativo com o Banco de Dados do Azure para MySQL
description: Tutorial de ponta a ponta para o Serviço de Aplicativo do Java com o MySQL
author: KarlErickson
ms.author: karler
ms.date: 11/12/2019
ms.service: app-service
ms.topic: article
ms.openlocfilehash: aa26952b320392beb553f327920fe5bd905a0b85
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74811822"
---
# <a name="deploy-a-spring-app-to-app-service-with-mysql"></a>Implantar um aplicativo Spring no Serviço de Aplicativo com o MySQL

Este tutorial explica o processo de criação, configuração, implantação, solução de problemas e dimensionamento de aplicativos Web do Java no Serviço de Aplicativo no Linux.

Este tutorial se baseia no conhecido aplicativo de exemplo Spring PetClinic. Neste tópico, você testará uma versão HSQLDB do aplicativo localmente e, em seguida, o implantará no [Serviço de Aplicativo do Azure](/azure/app-service/containers). Depois disso, você configurará e implantará uma versão que usa o [Banco de Dados do Azure para MySQL](/azure/mysql). Por fim, você aprenderá como acessar os logs de aplicativo e expandir, aumentando o número de funções de trabalho que executam seu aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* [CLI do Azure](https://docs.microsoft.com/cli/azure/overview)
* [Java 8](http://java.oracle.com/)
* [Maven 3](http://maven.apache.org/)
* [Git](https://github.com/)
* [Tomcat](https://tomcat.apache.org/download-80.cgi)
* [CLI do MySQL](http://dev.mysql.com/downloads/mysql/)

## <a name="get-the-sample"></a>Obter o exemplo

Para começar a usar o aplicativo de exemplo, clone e prepare o repositório de origem usando os comandos a seguir.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux.git
cd e2e-java-experience-in-app-service-linux
yes | cp -rf .prep/* .
```

## <a name="build-and-run-the-hsqldb-sample-locally"></a>Criar e executar o exemplo HSQLDB localmente

Primeiro, testaremos o exemplo localmente usando o HSQLDB como banco de dados.

Navegue até a versão HSQLDB do exemplo e compile-a.

```bash
cd initial-hsqldb/spring-framework-petclinic
mvn package
```

Em seguida, defina a variável de ambiente TOMCAT_HOME com o local da instalação do Tomcat.

```bash
export TOMCAT_HOME=<Tomcat install directory>
```

Em seguida, atualize o arquivo *pom.xml* para configurar o Maven para uma implantação de arquivo WAR do Tomcat. Adicione o XML a seguir como filho do elemento `<plugins>`. Se necessário, altere `1.7.7` para a versão atual do [Plug-in Cargo Maven 2](https://mvnrepository.com/artifact/org.codehaus.cargo/cargo-maven2-plugin).

```xml
<plugin>
    <groupId>org.codehaus.cargo</groupId>
    <artifactId>cargo-maven2-plugin</artifactId>
    <version>1.7.7</version>
    <configuration>
        <container>
            <containerId>tomcat8x</containerId>
            <type>installed</type>
            <home>${TOMCAT_HOME}</home>
        </container>
        <configuration>
            <type>existing</type>
            <home>${TOMCAT_HOME}</home>
        </configuration>
        <deployables>
            <deployable>
                <groupId>${project.groupId}</groupId>
                <artifactId>${project.artifactId}</artifactId>
                <type>war</type>
                <properties>
                    <context>/</context>
                </properties>
            </deployable>
        </deployables>
    </configuration>
</plugin>
```

Com essa configuração em vigor, você pode implantar o aplicativo localmente no Tomcat.

```bash
mvn cargo:deploy
```

Em seguida, inicie o Tomcat.

```bash
${TOMCAT_HOME}/bin/catalina.sh run
```

Agora você pode acessar [http://localhost:8080](http://localhost:8080) no seu navegador para ver o aplicativo em execução e ter uma ideia de como ele funciona. Ao terminar, selecione CTRL+C no prompt do Bash para interromper o Tomcat.

## <a name="deploy-to-azure-app-service"></a>Implantar no Serviço de Aplicativo do Azure

Agora que você o viu em execução localmente, implantaremos o aplicativo no Azure.

Primeiro, defina as variáveis de ambiente a seguir.

```bash
export RESOURCEGROUP_NAME=<resource group>
export WEBAPP_NAME=<web app>
export WEBAPP_PLAN_NAME=${WEBAPP_NAME}-appservice-plan
export REGION=<region>
```

O Maven usará esses valores para criar os recursos do Azure com os nomes que você fornecer. Usando variáveis de ambiente, você pode manter os segredos da sua conta fora dos arquivos de projeto.

Em seguida, atualize o arquivo *pom.xml* para configurar o Maven para uma implantação do Azure. Adicione o XML a seguir após o elemento `<plugin>` que você adicionou anteriormente. Se necessário, altere `1.7.0` para a versão atual do [Plug-in do Maven para o Serviço de Aplicativo do Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.8.0</version>
    <configuration>
        <schemaVersion>v2</schemaVersion>
        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>
        <runtime>
            <os>linux</os>
            <javaVersion>jre8</javaVersion>            
            <webContainer>TOMCAT 8.5</webContainer>
        </runtime>
    </configuration>
</plugin>
```

Em seguida, entre no Azure.

```azurecli
az login
```

Então, implante o aplicativo no Serviço de Aplicativo do Linux.

```bash
mvn azure-webapp:deploy
```

Agora você pode navegar até `https://<app-name>.azurewebsites.net` (após a substituição de `<app-name>`) para ver o aplicativo em execução.

## <a name="set-up-azure-database-for-mysql"></a>Configurar o Banco de Dados do Azure para MySQL

Em seguida, mudaremos para o MySQL em vez do HSQLDB. Criaremos uma instância do servidor do MySQL no Azure e adicionaremos um banco de dados. Em seguida, atualizaremos a configuração do aplicativo com as novas informações de conexão de banco de dados.

Primeiro, defina as variáveis de ambiente a seguir para uso em etapas posteriores.

```bash
export MYSQL_SERVER_NAME=<server>
export MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_NAME}.mysql.database.azure.com
export MYSQL_SERVER_ADMIN_LOGIN_NAME=<admin>
export MYSQL_SERVER_ADMIN_PASSWORD=<password>
export MYSQL_DATABASE_NAME=<database>
export DOLLAR=\$
```

Em seguida, crie e inicialize o servidor de banco de dados. Use [az mysql up](/cli/azure/ext/db-up/mysql?view=azure-cli-latest#ext-db-up-az-mysql-up) para a configuração inicial. Em seguida, use [az mysql server configuration set](/cli/azure/mysql/server/configuration?view=azure-cli-latest#az-mysql-server-configuration-set) para aumentar o tempo limite da conexão e definir o fuso horário do servidor.

```azurecli
az mysql up \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server-name ${MYSQL_SERVER_NAME} \
    --database-name ${MYSQL_DATABASE_NAME} \
    --admin-user ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
    --admin-password ${MYSQL_SERVER_ADMIN_PASSWORD}

az mysql server configuration set --name wait_timeout \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server ${MYSQL_SERVER_NAME} --value 2147483

az mysql server configuration set --name time_zone \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server ${MYSQL_SERVER_NAME} --value -8:00
```

Em seguida, use a CLI do MySQL para criar o banco de dados.

```bash
mysql -u ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
 -h ${MYSQL_SERVER_FULL_NAME} -P 3306 -p
```

No prompt da CLI do MySQL, execute o comando a seguir, substituindo `<database name>` pelo mesmo valor especificado anteriormente para a variável de ambiente `MYSQL_DATABASE_NAME`.

```console
CREATE DATABASE <database name>;
```

O MySQL agora está pronto para uso.

## <a name="configure-the-app-for-mysql"></a>Configurar o aplicativo para o MySQL

Em seguida, adicionaremos as informações de conexão à versão do MySQL do aplicativo e a implantaremos no Serviço de Aplicativo.

Primeiro, navegue até a pasta correta no prompt do Bash.

```bash
cd ../../initial-mysql/spring-framework-petclinic
```

Em seguida, atualize o arquivo *pom.xml* para tornar o MySQL a configuração ativa. Remova o `<activation>` elemento do perfil HSQLDB e coloque-o no perfil do MySQL, como mostrado aqui. O restante do snippet de código mostra a configuração existente. Observe como as variáveis de ambiente definidas anteriormente são usadas pelo Maven para configurar o acesso do MySQL.

```xml
<profile>
    <id>MySQL</id>
    <activation>
        <activeByDefault>true</activeByDefault>
    </activation>
    <properties>
        <db.script>mysql</db.script>
        <jpa.database>MYSQL</jpa.database>
        <jdbc.driverClassName>com.mysql.jdbc.Driver</jdbc.driverClassName>
        <jdbc.url>jdbc:mysql://${DOLLAR}{MYSQL_SERVER_FULL_NAME}:3306/${DOLLAR}{MYSQL_DATABASE_NAME}?useUnicode=true</jdbc.url>
        <jdbc.username>${DOLLAR}{MYSQL_SERVER_ADMIN_LOGIN_NAME}</jdbc.username>
        <jdbc.password>${DOLLAR}{MYSQL_SERVER_ADMIN_PASSWORD}</jdbc.password>
    </properties>
    ...
</profile>
```

Em seguida, atualize o arquivo *pom.xml* para configurar o Maven para uma implantação do Azure e para uso do MySQL. Adicione o XML a seguir após o elemento `<plugin>` que você adicionou anteriormente. Se necessário, altere `1.7.0` para a versão atual do [Plug-in do Maven para o Serviço de Aplicativo do Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.7.0</version>
    <configuration>

        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>

        <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>

        <appSettings>
            <property>
                <name>MYSQL_SERVER_FULL_NAME</name>
                <value>${MYSQL_SERVER_FULL_NAME}</value>
            </property>
            <property>
                <name>MYSQL_SERVER_ADMIN_LOGIN_NAME</name>
                <value>${MYSQL_SERVER_ADMIN_LOGIN_NAME}</value>
            </property>
            <property>
                <name>MYSQL_SERVER_ADMIN_PASSWORD</name>
                <value>${MYSQL_SERVER_ADMIN_PASSWORD}</value>
            </property>
            <property>
                <name>MYSQL_DATABASE_NAME</name>
                <value>${MYSQL_DATABASE_NAME}</value>
            </property>
        </appSettings>

    </configuration>
</plugin>
```

Em seguida, compile o aplicativo e teste-o localmente implantando e executando com o Tomcat.

```bash
mvn package
mvn cargo:deploy
${TOMCAT_HOME}/bin/catalina.sh run
```

Agora você pode exibir o aplicativo localmente em [http://localhost:8080](http://localhost:8080). O aplicativo terá a mesma aparência e se comportará da mesma forma que antes, mas usando o Banco de Dados do Azure para MySQL em vez do HSQLDB. Ao terminar, selecione CTRL+C no prompt do Bash para interromper o Tomcat.

Por fim, implante o aplicativo no Serviço de Aplicativo.

```bash
mvn azure-webapp:deploy
```

Agora você pode navegar até `https://<app-name>.azurewebsites.net` para ver o aplicativo em execução usando o Serviço de Aplicativo e o Banco de Dados do Azure para MySQL.

## <a name="access-the-app-logs"></a>Acessar os logs de aplicativo

Se precisar solucionar problemas, você poderá examinar os logs de aplicativo. Para abrir o fluxo de log remoto em seu computador local, use o comando a seguir.

```azurecli
az webapp log tail --name ${WEBAPP_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

Quando terminar de exibir os logs, selecione CTRL+C para interromper o fluxo.

O fluxo de log também está disponível em `https://<app-name>.scm.azurewebsites.net/api/logstream`.

## <a name="scale-out"></a>Expansão

Para oferecer suporte a um maior tráfego em seu aplicativo, você pode expandir para várias instâncias usando o comando a seguir.

```azurecli
az appservice plan update --number-of-workers 2 \
    --name ${WEBAPP_PLAN_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

Parabéns! Você criou e expandiu um aplicativo Web Java usando o Spring Framework, o JSP, o Spring Data, o Hibernate, o JDBC, o Serviço de Aplicativo do Linux e o Banco de Dados do Azure para MySQL.

## <a name="clean-up-resources"></a>Limpar recursos

Nas seções anteriores, você criou recursos do Azure em um grupo de recursos. Se você acha que não precisará usar esses recursos no futuro, exclua o grupo de recursos executando o comando a seguir.

```azurecli
az group delete --name ${RESOURCEGROUP_NAME}
```

## <a name="next-steps"></a>Próximas etapas

Em seguida, confira as outras opções de configuração e CI/CD disponíveis para Java com o Serviço de Aplicativo.

> [!div class="nextstepaction"]
> [Configurar um aplicativo Java do Linux para o Serviço de Aplicativo do Azure](/azure/app-service/containers/configure-language-java)
> [!div class="nextstepaction"]
> [Criar e implantar em um aplicativo Web do Java usando o Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp?view=azure-devops&tabs=java-tomcat)
> [!div class="nextstepaction"]
> [Implantar no Serviço de Aplicativo do Azure usando o plug-in Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)
