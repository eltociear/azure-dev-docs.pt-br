---
author: yevster
ms.author: yebronsh
ms.topic: include
ms.date: 1/20/2020
ms.openlocfilehash: 8c2ada1a8a11e1f0ae108e4356434825a2cf0b76
ms.sourcegitcommit: 3585b1b5148e0f8eb950037345bafe6a4f6be854
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288555"
---
### <a name="inventory-external-resources"></a>Recursos externos de inventário

Recursos externos, como fontes de dados, agentes de mensagem JMS e outros, são injetados por meio de JNDI (Interface de Nomenclatura e Diretório do Java). Alguns desses recursos podem exigir migração ou reconfiguração.

#### <a name="inside-your-application"></a>Dentro de seu aplicativo

Inspecione o arquivo *META-INF/context.xml*. Procure elementos de `<Resource>` dentro do elemento `<Context>`.

#### <a name="on-the-application-servers"></a>Nos servidores de aplicativos

Inspecione os arquivos *$CATALINA_BASE/conf/context.xml* e *$CATALINA_BASE/conf/server.xml*, bem como os arquivos *.xml* encontrados nos diretórios *$CATALINA_BASE/conf/[nome-do-mecanismo]/[nome-do-host]* .

Em arquivos *context.xml*, os recursos de JNDI serão descritos pelos elementos `<Resource>` dentro do elemento `<Context>` de nível superior.

Em arquivos *server.xml*, os recursos de JNDI serão descritos pelos elementos `<Resource>` dentro do elemento `<GlobalNamingResources>`.

#### <a name="datasources"></a>Fontes de dados

Fontes de dados são recursos de JNDI com o atributo `type` definido como `javax.sql.DataSource`. Para cada fonte de dados, documente as seguintes informações:

* Qual é o nome da fonte de dados?
* Qual é a configuração do pool de conexões?
* Onde posso encontrar o arquivo JAR do driver JDBC?

#### <a name="all-other-external-resources"></a>Todos os outros recursos externos

Não é possível documentar todas as dependências externas possíveis neste guia. É responsabilidade da sua equipe verificar se você pode atender a todas as dependências externas de seu aplicativo após a migração.
