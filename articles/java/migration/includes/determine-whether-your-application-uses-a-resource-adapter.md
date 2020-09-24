---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 9fa87bf3d0de64271a9ffb0e7e8fbff3cd1afd12
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738438"
---
### <a name="determine-whether-your-application-uses-a-resource-adapter"></a>Determinar se seu aplicativo usa um Adaptador de Recurso

Se o aplicativo precisar de um RA (Adaptador de Recurso), ele precisará ser compatível com o WildFly. Determine se o RA funciona bem em uma instância autônoma do WildFly implantando-o no servidor e configurando-o corretamente. Se o RA funcionar corretamente, você precisará adicionar os JARs ao classpath do servidor da imagem do Docker e colocar os arquivos de configuração necessários no local correto nos diretórios do servidor do WildFly para que ele fique disponível.
