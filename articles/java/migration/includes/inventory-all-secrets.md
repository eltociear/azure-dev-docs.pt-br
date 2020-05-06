---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: a771d0689e83e0b61bf9b8c31e0cbf36949fdc20
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673392"
---
### <a name="inventory-all-secrets"></a>Inventariar todos os segredos

Antes do advento das tecnologias de "configuração como serviço", como o Azure Key Vault, não havia um conceito bem definido de "segredos". Em vez disso, você tinha um conjunto distinto de definições de configuração que funcionavam efetivamente como aquilo que agora chamamos de "segredos". Com servidores de aplicativos como o WebLogic Server, esses segredos estão em muitos arquivos de configuração e repositórios de configuração diferentes. Verifique todas as propriedades e os arquivos de configuração nos servidores de produção em busca de segredos e senhas. Não se esqueça de verificar o *weblogic.xml* em seus WARs. Arquivos de configuração que contenham senhas ou credenciais também podem ser encontrados dentro de seu aplicativo. Para saber mais, consulte [Conceitos básicos do Azure Key Vault](/azure/key-vault/basic-concepts).
