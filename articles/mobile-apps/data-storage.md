---
title: Armazene, gerencie e persista dados de aplicativos na nuvem com o Visual Studio App Center e os serviços do Azure
description: Saiba mais sobre serviços como o Visual Studio App Center que permitem armazenar, gerenciar e persistir dados de aplicativos móveis na nuvem.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 1fc4fa3118368bd459024d894094ac2eac4b1b71
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85790663"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Armazene, sincronize e consulte dados de aplicativos móveis da nuvem

Independentemente do tipo de aplicativo criado, provavelmente você vai gerar e processar dados. Os usuários do seu aplicativo têm altas expectativas. Eles querem que o aplicativo funcione de forma rápida e direta, em todas as circunstâncias. A maioria dos aplicativos também funciona em vários dispositivos. Você pode acessar seu aplicativo por um dispositivo móvel ou desktop. Vários usuários podem usar o aplicativo ao mesmo tempo e compartilhar dados com a expectativa de obter acesso imediato e em tempo real aos dados.

Os usuários do aplicativo nem sempre terão conectividade com a Internet. Os aplicativos são projetados e devem funcionar com ou sem uma conexão com a Internet. Os desenvolvedores devem escolher a solução certa para armazenar dados na nuvem e sincronizá-los com ela para fornecer uma excelente experiência do cliente para o aplicativo deles, que pode incluir o desenvolvimento de um armazenamento de dados offline próprio.

Para fornecer a experiência mais rica possível, a Microsoft oferece uma variedade de serviços que eliminam a necessidade de criar servidores, escolher um banco de dados ou se preocupar com escala ou segurança. Esses serviços fornecem uma excelente experiência de desenvolvedor que permite armazenar dados de aplicativos na nuvem através do uso de as APIs SQL ou NoSQL. Também é possível sincronizar dados em todos os dispositivos e permitir que o aplicativo funcione com ou sem uma conexão de rede para ajudar a criar aplicativos escalonáveis e robustos.

Use os seguintes serviços para gerenciar e armazenar dados de aplicativos móveis na nuvem.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é um serviço de banco de dados multimodelo globalmente distribuído. Você pode usá-lo para criar aplicativos em escala mundial. Com o Azure Cosmos DB, você pode dimensionar de forma elástica e independente a taxa de transferência e o armazenamento em qualquer número de regiões do Azure em todo o mundo. Usando suas superfícies de API favoritas, você pode aproveitar o acesso a dados rápido e em um valor de milissegundo de um dígito. Essas superfícies incluem SQL, MongoDB, Cassandra, Tables ou Gremlin. O Azure Cosmos DB fornece exclusivamente SLAs (contratos de nível de serviço) para taxa de transferência, latência, disponibilidade e consistência.

### <a name="azure-cosmos-db-features"></a>Recursos do Azure Cosmos DB

- Suporte a uma ampla gama de APIs, que inclui a API do SQL (núcleo), API do Cassandra, API do MongoDB, API Gremlin e API de Tabela.
- A distribuição global turnkey replica seus dados onde quer que os usuários estejam. Os usuários podem interagir com a réplica dos dados mais próxima deles.
- Nenhum gerenciamento de esquema nem de índice porque o mecanismo de banco de dados é totalmente independente de esquema.
- Presença regional onipresente porque o Azure Cosmos DB está disponível em todas as regiões do Azure em todo o mundo, e isso inclui mais de 54 regiões na nuvem pública.
- Precisamente definidas, várias opções de consistência porque o protocolo de replicação de vários mestres do Azure Cosmos DB é projetado cuidadosamente para oferecer cinco opções de consistência bem definidas. Essas cinco opções são: forte, desatualização limitada, sessão, prefixo consistente e eventual.
- 99,999% de disponibilidade para leituras e gravações.
- Invocação programática (ou por meio do portal do Azure) do failover regional da sua conta do Azure Cosmos DB para garantir que seu aplicativo seja projetado para resistir a um desastre regional.
- Baixa latência garantida no 99º percentil, no mundo todo.

### <a name="azure-cosmos-db-references"></a>Referências do Azure Cosmos DB

- [Azure portal](https://portal.azure.com) 
- [Documentação do BD Cosmos do Azure](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

 O [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) é um serviço gerenciado de banco de dados relacional de uso geral. É possível usá-lo para criar uma camada de armazenamento de dados altamente disponível e de alto desempenho para aplicativos e soluções na nuvem do Azure.

### <a name="azure-sql-database-features"></a>Recursos do Banco de Dados SQL do Azure

- **Ferramentas e modelos de banco de dados elástico:** Com um banco de dados elástico, os desenvolvedores podem agrupar recursos entre um grupo de bancos de dados para fins de dimensionamento. Para gerenciar de forma administrativa esses recursos, você envia um script como um trabalho. Em seguida, o banco de dados SQL executa o script em todos os bancos de dados.
- **Alto desempenho:** Aplicativos de alta taxa de transferência podem aproveitar a versão mais recente. Ele fornece 25% mais potência de banco de dados premium.
- **Backups, replicação e alta disponibilidade:** A replicação interna e um SLA com suporte da Microsoft no nível do banco de dados fornecem continuidade de aplicativos e proteção contra eventos catastróficos. A replicação geográfica ativa permite que você configure o failover e a restauração de autoatendimento, que fornecem controle total sobre "recuperação de ops". A restauração de dados está disponível em backups de dados de até 35 dias.
- **Manutenção quase zero:** O software automático faz parte do serviço. As réplicas internas do sistema ajudam a fornecer proteção de dados inerente, tempo de atividade do banco de dados e estabilidade do sistema. As réplicas do sistema são automaticamente movidas para novos computadores. Elas são provisionadas em tempo real à medida que as antigas falham.
- **Segurança:** O Banco de Dados SQL do Azure oferece um portfólio de recursos de segurança para atender às políticas de conformidade da organização ou das normas do setor:

- A auditoria fornece aos desenvolvedores a capacidade de executar tarefas relacionadas à conformidade e obter conhecimento sobre atividades.
- Os desenvolvedores e os técnicos de TI podem implementar políticas no nível de banco de dados para ajudar a limitar o acesso a dados confidenciais com segurança em nível de linha, mascaramento de dados dinâmicos e Transparent Data Encryption para o Banco de Dados SQL do Azure.
- O Banco de Dados SQL do Azure é verificado pelos principais auditores de nuvem como parte do escopo das principais certificações e aprovações de conformidade do Azure, como HIPAA BAA, ISO/IEC 27001:2005, FedRAMP e cláusulas-modelo da UE.

### <a name="azure-sql-database-references"></a>Referências do Banco de Dados SQL do Azure

- [Azure portal](https://portal.azure.com) 
- [Documentação do Banco de Dados SQL do Azure](/azure/sql-database/)
