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
ms.openlocfilehash: 0730cec8470a3b55421c6c0cafa08f88819cb1d8
ms.sourcegitcommit: 6012460ad8d6ff112226b8f9ea6da397ef77712d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72279081"
---
# <a name="parameters-for-operation-configuration"></a>Parâmetros para configuração da operação

É possível fornecer parâmetros extras para métodos em operações no SDK do Azure para Python.

Os parâmetros extras são fornecidos no `kwargs`. Essa funcionalidade é denominada *operation_config*.

As opções de configuração da operação são:

|Nome do parâmetro|Type|Função|
|----------------------|------|---------------|
| verificar |`bool`|Se deseja verificar o certificado SSL. Padrão: True.|
|  cert |`str`| Caminho para o certificado local para verificação do lado do cliente.|
|  Tempo limite |`int`| Tempo limite para estabelecer uma conexão de servidor em segundos.|
|  allow_redirects |`bool` | Se deseja permitir redirecionamentos.|
|  max_redirects  |`int`| Número máximo de redirecionamentos permitidos.|
|  proxies  |`dict` |Configurações do servidor de proxy.|
|  use_env_proxies |`bool` |Se deseja ler as configurações de proxy de variáveis de ambiente local.|
|  retries  |`int` | O número total de novas tentativas.|
|  enable_http_logger | `bool`| Habilite logs de HTTP no modo de depuração (False por padrão).|
