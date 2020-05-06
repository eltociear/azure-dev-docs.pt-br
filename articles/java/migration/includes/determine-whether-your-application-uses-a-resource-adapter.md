---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: f5995a3c5efc46bc58b446589ce089bf7d86b2ba
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673072"
---
### <a name="determine-whether-your-application-uses-a-resource-adapter"></a>Determinar se seu aplicativo usa um Adaptador de Recurso

Se o aplicativo precisar de um RA (Adaptador de Recurso), ele precisará ser compatível com o WildFly. Determine se o RA funciona bem em uma instância autônoma do WildFly implantando-o no servidor e configurando-o corretamente. Se o RA funcionar corretamente, você precisará adicionar os JARs ao classpath do servidor da imagem do Docker e colocar os arquivos de configuração necessários no local correto nos diretórios do servidor do WildFly para que ele fique disponível.
