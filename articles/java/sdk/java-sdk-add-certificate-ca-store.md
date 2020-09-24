---
title: Adicionar um certificado raiz do Azure ao repositório CA de Java
description: Saiba como adicionar um certificado raiz de CA (autoridade de certificação) para o repositório de certificado CA (cacerts) de Java para uso com o Microsoft Azure.
documentationcenter: java
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/13/2018
ms.custom: devx-track-java
ms.openlocfilehash: 2f40f013d7323c51c25d5ecf97f097824d75e914
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831672"
---
# <a name="adding-a-root-certificate-to-the-java-ca-certificates-store"></a>Adicionar um certificado raiz ao repositório de certificados CA de Java

Aplicativos que usam os serviços do Azure (por exemplo, o Barramento de Serviço do Azure) precisam confiar no certificado raiz de Baltimore CyberTrust. Talvez esse certificado já esteja instalado em seu sistema, mas se não estiver, as etapas neste tutorial mostrarão como usar o **keytool** da Oracle para adicionar o certificado raiz da autoridade de certificação (CA) necessário para o repositório de certificados de Java (cacerts) que você usará para os serviços do Azure.

O utilitário de keytool da Oracle é uma _Ferramenta de gerenciamento de certificado e chave_ que permite aos desenvolvedores gerenciar a lista de certificados confiáveis para uso com Java. Você pode usar o keytool para adicionar o certificado de autoridade de certificação antes de compactar seu JDK e adicionar a sua pasta *approot* do projeto do Azure ou executar uma tarefa de inicialização do Azure que usa keytool para adicionar o certificado.

A partir de 15 de abril de 2013, o Azure começou a migrar do certificado raiz GTE CyberTrust Global para o certificado raiz Baltimore CyberTrust. As etapas a seguir mostram como usar o keytool para adicionar o certificado raiz Baltimore CyberTrust ao repositório de certificados de CA (cacerts) para Java.

> [!NOTE]
> Use as etapas neste artigo para configurar o SDK do Java para confiar nos certificados raiz de outras autoridades de certificação confiáveis. Por exemplo, você pode optar por um certificado raiz da lista de certificados em [Certificados raiz da GeoTrust](https://www.geotrust.com/resources/root-certificates/).

## <a name="determining-which-root-certificates-are-installed"></a>Determinar quais certificados raiz estão instalados

O certificado Baltimore já pode estar instalado em seu repositório cacerts, portanto, você precisará usar as etapas a seguir para determinar se ele já foi instalado.

1. Em um prompt de comando de administrador, navegue até a pasta *jdk\jre\lib\security* de seu JDK e execute o seguinte comando para listar os certificados que estão instalados em seu sistema:

   ```shell
   keytool -list -keystore cacerts
   ```

1. Se você for solicitado a inserir a senha do repositório, a senha padrão será `changeit`.

   > [!NOTE]
   > Se você quiser alterar a senha do repositório, confira a documentação do keytool em <https://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

1. Se você não vir o certificado com a impressão digital do `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`, use as etapas na seção a seguir para baixar e instalar o certificado.

## <a name="to-add-a-root-certificate-to-the-cacerts-store"></a>Para adicionar um certificado raiz ao repositório cacerts

1. Baixe o certificado raiz Baltimore CyberTrust em <https://cacert.omniroot.com/bc2025.crt> e salve em um arquivo local com a extensão *.crt* em sua pasta *jdk\jre\lib\security*. Para este exemplo, vamos supor que você baixou o arquivo do certificado raiz do Baltimore CyberTrust como *bc2025.crt*.

   > [!NOTE]
   > O certificado raiz Baltimore CyberTrust tem um número de série `02:00:00:b9` e uma impressão digital SHA1 de `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Importe o certificado para o repositório cacerts usando o seguinte comando:

   ```shell
   keytool -keystore cacerts -importcert -alias bc2025ca -file bc2025.crt
   ```

   Em que:

   |  Parâmetro   |                              Descrição                               |
   |--------------|------------------------------------------------------------------------|
   | `keystore`   | Especifica o repositório de certificados.                                       |
   | `importcert` | Especifica que você está importando um certificado.                        |
   | `alias`      | Especifica um alias para o certificado.                                |
   | `file`       | Especifica o nome do arquivo do certificado raiz que você está importando. |

3. Se você for solicitado a confiar no certificado, verifique a impressão digital como `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74` e digite `y` se ela estiver correta.

4. Execute o seguinte comando para garantir que o certificado de CA foi importado com êxito:

   ```shell
   keytool -list -keystore cacerts
   ```

Após adicionar com êxito o certificado raiz ao seu JDK, compacte o conteúdo do JDK e adicione-o à pasta *approot* do projeto do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o utilitário keytool, confira <https://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

Para saber mais sobre Java, veja [Centro de desenvolvedores do Java](../index.yml).

Para obter mais informações sobre os JDKs com suporte disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.