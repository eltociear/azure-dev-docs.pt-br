---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 20a8fa10a27a41621c2b08839682fefbd023ee2e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401313"
---
Um back-end de [API Web do ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) é usado para manipular o [registro do dispositivo](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) para o cliente usando a melhor e mais recente abordagem de [Instalação](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations). O serviço também enviará notificações por push de maneira multiplataforma. 

Essas operações são manipuladas usando o [SDK dos Hubs de Notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Mais detalhes sobre a abordagem geral são fornecidos na documentação [Registrar usando o back-end do aplicativo](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend).
