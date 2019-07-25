---
title: SDK do Azure para configuração de operações do Python
description: C gerado pelo SDK do Azure para Python
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/07/2018
ms.topic: conceptual
ms.devlang: python
ms.openlocfilehash: 9638aa4602f96e2da0155a7b3840e5be4857eb98
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68285507"
---
# <a name="operation-config"></a>Configuração de operação 

Os métodos em operações têm parâmetros adicionais que podem ser fornecidos no `kwargs`. Isso é chamado de operation_config.

As opções de configuração da operação são:

|Nome do parâmetro|Type|Função|
|----------------------|------|---------------|
| verificar |`bool`|Se deseja verificar o certificado SSL. Padrão: True.|
|  cert |`str`| Caminho até o certificado local para verificação do lado do cliente.|
|  Tempo limite |`int`| Tempo limite para estabelecer uma conexão de servidor em segundos.|
|  allow_redirects |`bool` | Se deseja permitir redirecionamentos.|
|  max_redirects  |`int`| O número máximo de redirecionamentos permitidos.|
|  proxies  |`dict` |Configurações do servidor de proxy.|
|  use_env_proxies |`bool` |Se deseja ler as configurações de proxy de variáveis de ambiente local.|
|  retries  |`int` | O número total de novas tentativas.|
|  enable_http_logger | `bool`| Habilite logs de HTTP no modo de depuração (False por padrão).|
