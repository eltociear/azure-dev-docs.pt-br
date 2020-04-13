---
title: Usando o Chef com o Azure
description: Introdução para usar o Chef para configurar e testar sua infraestrutura do Azure
keywords: azure, chef, devops, máquinas virtuais, visão geral, automatizar
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 94cfe5d57673db151d53133fb8b2ef66c5629b93
ms.sourcegitcommit: a32ca0946275165ce24216c6fa243ec21d6c9193
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892985"
---
# <a name="using-chef-with-azure"></a>Usando o Chef com o Azure

[Chef](https://www.chef.io) é uma plataforma de automação avançada que transforma a infraestrutura da máquina virtual no Azure no código. O Chef automatiza como a infraestrutura é configurada, implantada e gerenciada em sua rede, independentemente de seu tamanho.

Este artigo descreve os benefícios de usar o Chef para gerenciar a infraestrutura do Azure.

## <a name="chef-extension-on-azure"></a>Extensão da Chef no Azure

Provisione uma máquina virtual com o cliente do Chef em execução como um serviço em segundo plano com a [Extensão da Chef](https://docs.microsoft.com/azure/chef/chef-extension-portal) no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040). Depois de provisionadas, essas máquinas virtuais estão prontas para serem gerenciadas por um servidor da Chef.

## <a name="chef-cloud-shell"></a>Cloud Shell da Chef

Use uma estação de trabalho da Chef diretamente no Azure Cloud Shell! Execute todos os seus utilitários da Chef e o InSpec diretamente do Cloud Shell. Você pode utilizar os comandos da Chef de:

* [chef](https://docs.chef.io/ctl_chef.html)
* [kitchen](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Combine nossos utilitários de comando com as ferramentas disponíveis no Cloud Shell, tais como `git`, `az-cli`, e `terraform`, e escreva sua automação de infraestrutura e de conformidade por meio do navegador.

## <a name="automate-everything-with-one-platform"></a>Automatizar tudo com uma plataforma

As empresas requerem velocidade e segurança para competir no mercado digital. Chef e Microsoft juntos ajudam pessoas, equipes e negócios a realizar todas essas coisas. Com uma única plataforma, o Chef Automate, você pode agora automatizar e entregar continuamente sua infraestrutura, aplicativos e conformidade pelo seu estado Microsoft.

## <a name="test-drive-chef-automate-on-azure"></a>Test drive da Automatização do Chef no Azure

Suportado pelo Chef, a [solução Chef Automate Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) possibilita que você construa, implante e gerencia sua infraestrutura e aplicativos colaborativamente. Com um clique, você obtém acesso a todos os recursos comerciais incluídos no Chef Automate, obtém visibilidade de ponta a ponta da sua frota inteira, habilita a conformidade contínua e gerencia todas as alterações com um fluxo de trabalho unificado.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma Máquina Virtual do Windows no Azure usando o Chef](windows-vm-configure.md)
