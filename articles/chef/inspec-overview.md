---
title: Use InSpec para a automação de conformidade de sua infraestrutura do Azure
description: Saiba como usar o InSpec para detectar problemas em implantações do Microsoft Azure
keywords: azure, chef, devops, virtual machines, overview, automate, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 096ea093c96187a4a88ba4ce408d2cf2edbf469d
ms.sourcegitcommit: a32ca0946275165ce24216c6fa243ec21d6c9193
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892995"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Use InSpec para a automação de conformidade de sua infraestrutura do Azure

O [InSpec](https://www.chef.io/inspec/) é a linguagem de software livre do Chef para descrever as regras de segurança e conformidade que podem ser compartilhadas entre engenheiros de software, operações e engenheiros de segurança. O InSpec funciona comparando o estado real do sua infraestrutura com o estado desejado que você expressa no código fácil de ler e fácil de gravar do InSpec. InSpec detecta violações e exibe os resultados na forma de um relatório, mas coloca você no controle de correção.

Você pode usar o InSpec para validar o estado dos recursos e grupos de recursos em uma assinatura, incluindo máquinas virtuais, configurações de rede, configurações do Azure Active Directory e muito mais.

Este artigo descreve os benefícios de usar o InSpec para facilitar a segurança e conformidade no Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Tornar a conformidade fácil de entender e avaliar

A documentação de conformidade escrita em planilhas ou documentos do Word deixa os requisitos abertos a interpretações. Com o InSpec, é possível transformar seus requisitos em código com controle de versão, executável, legível. O código substitui conversas sobre o que deve ser avaliado em favor de testes tangíveis com uma intenção clara.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Detectar problemas de todo o fleet e priorizar a sua correção

O InSpec sem agente detecta o modo que permite a você avaliar, rapidamente e em escala, o seu nível de exposição. Metadados internas para classificação de severidade/impacto ajudam a determinar quais áreas de foco para correção. Você também pode escrever regras rapidamente em resposta a novas vulnerabilidades ou normas governamentais e implantá-las imediatamente.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Auditar máquinas virtuais do Azure com Configuração de Convidado da política

O Azure é diretamente compatível com o uso de definições do InSpec do Chef para auditar máquinas virtuais do Azure por meio da [Configuração de Convidado do Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). A Configuração de Convidado avalia uma máquina virtual do Linux quanto a uma determinada definição do InSpec do Chef e relata a conformidade de acordo com o Azure Policy. Os resultados dessas auditorias também são relatados por meio de logs do Azure Monitor, possibilitando alertas e outros cenários de automação.

## <a name="satisfy-audits"></a>Satisfazer auditorias

Com InSpec, você pode responder às perguntas de auditoria a qualquer momento - não apenas em intervalos predeterminados como trimestral ou anual. Executando testes do InSpec continuamente, você entra em um ciclo de auditoria sabendo seu histórico e sua postura de conformidade exatos, em vez de ser surpreendido pelas descobertas de um auditor.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Experimentar o InSpec no Azure Cloud Shell](https://shell.azure.com)