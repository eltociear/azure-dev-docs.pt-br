---
title: 'Passo a passo, parte 2: autenticar aplicativos Python com os serviços do Azure'
description: Uma discussão sobre as diferentes necessidades de autenticação e os desafios no cenário de exemplo e como esses desafios são atendidos com a autenticação integrada do Azure.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: c6719e3c86b590edff551d98e5a961fd08f857c3
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379457"
---
# <a name="part-2-authentication-needs-in-the-scenario"></a>Parte 2: necessidades de autenticação no cenário

[Página anterior: introdução e segundo plano](walkthrough-tutorial-authentication-01.md)

Neste cenário de exemplo, o aplicativo principal tem os seguintes requisitos de autenticação:

- Autentique com Azure Key Vault para acessar a chave de API de terceiros armazenada.
- Autentique com a API de terceiros usando a chave de API.
- Autentique com o Armazenamento de Filas do Azure usando as credenciais necessárias para a conta de armazenamento.

Com esses três requisitos distintos, o aplicativo precisa gerenciar três conjuntos de credenciais: dois para os recursos do Azure (Key Vault e Armazenamento de Filas) e outro para um recurso externo (a API de terceiros).

Conforme observado anteriormente, você pode gerenciar com segurança todas as credenciais no Key Vault, exceto aquelas necessárias para o próprio Key Vault. Uma vez autenticado com Key Vault, o aplicativo pode recuperar qualquer outra chave em tempo de execução para autenticar com serviços como o Armazenamento de Fila.

Essa abordagem, no entanto, ainda requer que o aplicativo gerencie as credenciais do Key Vault separadamente. Como, então, você pode fazer para gerenciar essas credenciais de maneira segura e que funcione para o desenvolvimento local e para sua implantação de produção na nuvem?

Uma solução parcial é armazenar a chave em uma variável de ambiente do lado do servidor (ou seja, por meio de uma configuração de aplicativo com o Serviço de Aplicativo do Azure e o Azure Functions), que pelo menos mantém a chave fora do controle do código-fonte. No entanto, para executar o código em uma estação de trabalho de desenvolvedor, você deve replicar essa variável de ambiente localmente, que arrisca a exposição das credenciais e/ou a inclusão acidental no controle do código-fonte. Você pode contornar o problema em alguma medida implementando procedimentos especiais na versão de desenvolvimento do seu código, mas isso adiciona complexidade ao seu processo de desenvolvimento.

Felizmente, a autenticação integrada com o Azure AD (Active Directory) permite que um aplicativo evite manipular qualquer credencial do Azure.

## <a name="integrated-authentication-with-managed-identity"></a>Autenticação integrada com identidade gerenciada

Muitos serviços do Azure, como o Armazenamento e o Key Vault, são integrados ao Azure AD (Azure Active Directory), de modo que, ao autenticar o aplicativo com o Azure AD usando uma [identidade gerenciada](/azure/active-directory/managed-identities-azure-resources/overview), ele é automaticamente autenticado com outros recursos conectados. A autorização da identidade é manipulada por meio de [RBAC (controle de acesso baseado em função)](how-to-assign-role-permissions.md) e, ocasionalmente, por meio de outras políticas de acesso.

Essa integração significa que você nunca precisa lidar com credenciais relacionadas ao Azure no código do aplicativo e essas credenciais nunca aparecem nas estações de trabalho do desenvolvedor ou no controle do código-fonte. Além disso, qualquer manipulação de chaves para APIs e serviços de terceiros é feita inteiramente em tempo de execução, mantendo assim essas chaves em segurança.

A identidade gerenciada funciona especificamente com aplicativos que são implantados no Azure. Para o desenvolvimento local, você cria uma entidade de serviço separada para servir como a identidade do aplicativo ao ser executado localmente. Você disponibiliza essa entidade de serviço para as bibliotecas do Azure usando variáveis de ambiente, conforme descrito em [Configurar seu ambiente de desenvolvimento local – configurar a autenticação](configure-local-development-environment.md#configure-authentication). Você também atribui permissões de função a essa entidade de serviço, juntamente com a identidade gerenciada usada na nuvem.

Depois de executar essas etapas para a entidade de serviço local, o mesmo código funcionará localmente e na nuvem para autenticar o aplicativo com os recursos do Azure. Esses detalhes são discutidos em [Como autenticar e autorizar aplicativos](azure-sdk-authenticate.md), mas a versão resumida é a seguinte:

1. Em seu código, crie um objeto `DefaultAzureCredential` que usa automaticamente sua identidade gerenciada ao ser executado no Azure e sua entidade de serviço separada ao ser executado localmente.

1. Use essa credencial ao criar o objeto de cliente apropriado para qualquer recurso que quiser acessar (Key Vault, Armazenamento de Filas etc.).

1. Em seguida, a autenticação ocorre quando você chama um método de operação por meio do objeto de cliente, que gera uma chamada à API REST para o recurso.

1. Se a identidade do aplicativo for válida, o Azure também verificará se a identidade também está autorizada para a operação específica.

O restante deste tutorial demonstra todos os detalhes do processo no contexto do cenário de exemplo e do código de exemplo que o acompanha.

No script de provisionamento do exemplo, todos os recursos são criados em um grupo de recursos chamado `auth-scenario-rg`. Esse grupo é criado usando o comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) da CLI do Azure.

> [!div class="nextstepaction"]
> [Parte 3 – Exemplo de implementação de API de terceiros >>>](walkthrough-tutorial-authentication-03.md)
