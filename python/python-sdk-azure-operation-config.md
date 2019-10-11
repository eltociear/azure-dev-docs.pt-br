---
title: Parâmetros para configuração da operação – SDK do Azure para Python
description: C gerado pelo SDK do Azure para Python
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/07/2018
ms.topic: conceptual
ms.devlang: python
ms.custom: seo-python-october2019
ms.openlocfilehash: ca69b72789f28445c4654e635e641e2954890a38
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172370"
---
# <a name="parameters-for-operation-configuration"></a>Parâmetros para configuração da operação

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
