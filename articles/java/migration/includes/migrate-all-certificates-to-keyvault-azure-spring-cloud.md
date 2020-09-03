---
author: yevster
ms.author: yebronsh
ms.date: 7/17/2020
ms.openlocfilehash: 1e7957a03cb96254a0318774a1620c7143ae6fc4
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062037"
---
### <a name="migrate-all-certificates-to-keyvault"></a>Migrar todos os certificados para o Key Vault

O Azure Spring Cloud não fornece acesso ao repositório de chaves JRE. Portanto, migre os certificados para o Azure Key Vault e altere o código de aplicativo para acessar os certificados no Key Vault. Para obter mais informações, confira [Introdução aos certificados do Key Vault](/azure/key-vault/certificates/certificate-scenarios) e [Biblioteca de clientes de certificados do Azure Key Vault para Java](/java/api/overview/azure/security-keyvault-certificates-readme).