---
title: Notas de versão de bibliotecas de gerenciamento do Azure para Java | Microsoft Docs
description: Veja o que há de novo e fique de olho em alterações significativas nas bibliotecas de gerenciamento do Azure para Java
keywords: Azure, Java, API, referência, anotações, atualizações, substituir, obsoleto
ms.assetid: 1f128cf9-f747-4344-84e1-f9964709deb8
ms.topic: reference
ms.date: 3/06/2016
ms.openlocfilehash: 69c2c29935f9333dd1d31b26b0941e0446ca5504
ms.sourcegitcommit: 3c69d7c3e5c5a00a01ee18e63b0659830c7d4ec0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82105197"
---
# <a name="release-notes"></a>Notas de versão 

## <a name="october-5-2017---130"></a>5 de outubro de 2017 - 1.3.0 

A versão 1.3.0 é compatível com versões anteriores para serviços e uso de recursos que atingiram o estágio de disponibilidade geral (estável) em versões anteriores.

As alterações de última hora de versões de visualização para esses serviços são marcadas com a anotação @Beta.


### <a name="generally-available-in-v13"></a>Em disponibilidade geral em V1.3

Algumas das APIs que ainda estavam em versões Beta anteriores estão agora na fase de Disponibilidade Geral (GA), em particular:

- métodos assíncronos
- todos os métodos na CDN que estavam anteriormente na versão Beta
- todos os métodos e interfaces no Gateway de Aplicativo que estavam anteriormente em versão Beta

  Algumas partes da biblioteca ainda estão em Versão Prévia. Consulte a tabela abaixo para saber o estado atual das bibliotecas:

Serviço ou recurso | Disponível como GA (disponibilidade geral) | Disponível como versão prévia 
---------|---------|---------|-
Computação  | Máquinas virtuais e extensões de VM, conjuntos de dimensionamento de máquina virtual, discos gerenciados   | Serviço de contêiner do Azure, registro de contêiner do Azure 
Armazenamento   |  Contas de armazenamento       |    Criptografia     
Banco de Dados SQL  | Bancos de dados, firewalls, pools elásticos              
Rede    |  Redes virtuais, adaptadores de rede, endereços IP, tabelas de roteamento, grupos de segurança de rede, DNS, gerenciadores de tráfego, gateways de aplicativo  |    Balanceadores de carga, Emparelhamento de rede, Gateway de Rede Virtual, Inspetores de rede 
Mais serviços    |  Gerenciador de Recursos, Cofre de Chaves, Redis, CDN, Lote       |  Aplicativos Web, Aplicativos de funções, Barramento de Serviço, Grafo RBAC, Cosmos DB, Pesquisar  
Conceitos básicos     |   Autenticação: núcleo, métodos assíncronos, identidade de serviço gerenciada      |      |

> Os recursos de visualização são marcados com uma anotação `@Beta` no nível de classe, interface ou método em bibliotecas. Esses recursos estão sujeitos a alterações. Eles podem ser modificados de alguma forma, ou até mesmo removidos, no futuro.

### <a name="import-with-maven"></a>Importar com Maven

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure</artifactId>
    <version>1.3.0</version>
</dependency>
```

### <a name="get-help-and-give-feedback"></a>Obter ajuda e fazer comentários

Confira a comunidade do [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-java-sdk) para obter ajuda com o uso das bibliotecas em seu próprio código. Caso encontre bugs ou tenha sugestões para aprimorar essas bibliotecas, relate problemas por meio do [GitHub](https://github.com/Azure/azure-sdk-for-java/issues).


