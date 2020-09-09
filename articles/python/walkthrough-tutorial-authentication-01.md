---
title: 'Passo a passo: autenticar aplicativos Python com os serviços do Azure'
description: Um passo a passo detalhado de como autenticar um aplicativo Python com o Azure Active Directory, o Azure Key Vault e o Armazenamento de Filas do Azure usando a biblioteca Azure-Identity do SDK do Python do Azure.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 7f716f3276c0b93ec37ba0941f4029b2ee817fa0
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379458"
---
# <a name="walkthrough-integrated-authentication-for-python-apps-with-azure-services"></a>Passo a passo: Autenticação integrada para aplicativos Python com os serviços do Azure

O Azure AD (Azure Active Directory), juntamente com o Azure Key Vault, fornece um meio abrangente e conveniente para que os aplicativos se autentiquem com os serviços do Azure, bem como com serviços de terceiros em que há chaves de acesso envolvidas.

Depois de fornecer um segundo plano, este passo a passo explica esses recursos de autenticação no contexto do exemplo, [github.com/Azure-Samples/python-integrated-authentication](https://github.com/Azure-Samples/python-integrated-authentication).

Para sua conveniência, o exemplo já foi implantado no Azure para que você possa vê-lo em operação. Se você quiser implantar o exemplo em sua própria assinatura do Azure, o repositório também inclui os scripts de implantação da CLI do Azure.

## <a name="part-1-background"></a>Parte 1: Segundo plano

Embora muitos serviços do Azure dependam exclusivamente do controle de acesso baseado em função para autorização, alguns serviços controlam o acesso aos respectivos recursos deles por meio de segredos ou chaves. Esses serviços incluem Armazenamento do Microsoft Azure, bancos de dados, Serviços Cognitivos, Key Vault e Hubs de Eventos.

Ao criar um aplicativo de nuvem que usa recursos nesses serviços, você usa o portal do Azure, a CLI do Azure ou o Azure PowerShell para criar e configurar chaves para o aplicativo, que estão vinculadas a políticas de acesso específicas. Essas chaves impedem o acesso aos recursos específicos do aplicativo por qualquer outro código não autorizado.

Nesse design geral, os aplicativos de nuvem normalmente devem gerenciar essas chaves e autenticar com cada serviço individualmente, um processo que pode ser entediante e propenso a erros. O gerenciamento de chaves diretamente no código do aplicativo também gera o risco de expor essas chaves no controle do código-fonte e as chaves podem ser armazenadas em estações de trabalho de desenvolvedor não seguras.

Felizmente, o Azure fornece dois serviços específicos para simplificar o processo e fornecer maior segurança:

- O Azure Key Vault fornece armazenamento seguro baseado em nuvem para chaves de acesso (juntamente com certificados e chaves de criptografia, que não são abordados neste artigo). Usando o Key Vault, o aplicativo acessa essas chaves somente em tempo de execução, de modo que elas jamais apareçam diretamente no código-fonte.

- Com as identidades gerenciadas do Azure AD (Azure Active Directory), o aplicativo precisa ser autenticado apenas uma vez com o Active Directory. Então, o aplicativo é autenticado automaticamente com outros serviços do Azure, incluindo o Key Vault. Como resultado, seu código jamais precisa se preocupar com chaves ou outras credenciais para esses serviços do Azure. Melhor ainda, você pode executar o mesmo código localmente e na nuvem com requisitos mínimos de configuração.

Usando juntos o Azure AD e o Key Vault, seu aplicativo jamais precisará se autenticar nos serviços individuais do Azure e poderá acessar com facilidade e segurança todas as chaves necessárias para os serviços de terceiros.

> [!IMPORTANT]
> Este artigo usa o termo comum e genérico "chave" para se referir ao que são armazenados como "segredos" no Azure Key Vault, como uma chave de acesso para uma API REST. Esse uso não deve ser confundido com o gerenciamento de chaves *criptográficas* do Key Vault, que é um recurso separado dos *segredos* do Key Vault.

## <a name="example-cloud-app-scenario"></a>Cenário de exemplo de aplicativo em nuvem

Para entender o processo de autenticação do Azure mais profundamente, considere o seguinte cenário:

- Um aplicativo principal expõe um ponto de extremidade de API (não autenticado) público que responde a solicitações HTTP com JSON. O ponto de extremidade de exemplo, conforme mostrado neste artigo, é implementado como um simples aplicativo do Flask, implantado no Serviço de Aplicativo do Azure.

- Para gerar a resposta, a API invoca uma API de terceiros que exige uma chave de acesso. O aplicativo recupera essa chave de acesso do Azure Key Vault em tempo de execução.

- Antes de retornar sua resposta, a API grava uma mensagem em uma Fila de Armazenamento do Microsoft Azure para processamento posterior. (O processamento específico dessas mensagens não é relevante para o cenário principal.)

![Diagrama do cenário do aplicativo](media/walkthrough-tutorial-authentication/scenario-diagram.png)

> [!NOTE]
> Embora um ponto de extremidade de API público seja geralmente protegido por sua própria chave de acesso, para os fins deste artigo, vamos supor que o ponto de extremidade esteja aberto e não autenticado. Essa suposição evita qualquer confusão entre as necessidades de autenticação do aplicativo e de um chamador *externo* desse ponto de extremidade. Esse cenário não demonstra tal chamador externo.

> [!div class="nextstepaction"]
> [Parte 2 – A autenticação precisa de >>>](walkthrough-tutorial-authentication-02.md)
