---
title: Tutorial – Configurar aplicativos no Serviço de Aplicativo do Azure usando o Ansible
description: Saiba como criar um aplicativo no Serviço de Aplicativo do Azure com Java 8 e o runtime do contêiner Tomcat
keywords: ansible, azure, devops, bash, guia estratégico, Serviço de Aplicativo do Azure, aplicativo Web, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 59113376d1f15d902553daa0c00bc7de56999d9d
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743700"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Tutorial: Configurar aplicativos no Serviço de Aplicativo do Azure usando o Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um aplicativo no Serviço de Aplicativo do Azure com Java 8 e o runtime do contêiner Tomcat
> * Criar um perfil do Gerenciador de Tráfego do Azure
> * Definir um ponto de extremidade do Gerenciador de Tráfego usando o aplicativo criado

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Criar um serviço de aplicativo básico

O código do guia estratégico nesta seção define os seguintes recursos:

* Grupo de recursos do Azure no qual o plano e o aplicativo do Serviço de Aplicativo são implantados
* Serviço de aplicativo no Linux com o Java 8 e o runtime do contêiner Tomcat

Salve o guia estratégico a seguir como `firstwebapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook firstwebapp.yml
```

Depois de executar o guia estratégico, você verá resultados semelhantes aos seguintes:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Criar um aplicativo e usar o Gerenciador de Tráfego do Azure

O [Gerenciador de Tráfego do Azure](/azure/app-service/web-sites-traffic-manager) permite controlar como as solicitações de clientes Web são distribuídas aos aplicativos no Serviço de Aplicativo do Azure. Quando os pontos de extremidade do Serviço de Aplicativo são adicionados a um perfil do Gerenciador de Tráfego do Azure, o Gerenciador de Tráfego rastreia o status dos aplicativos do Serviço de Aplicativo. Os status incluem em execução, parado e excluído. O Gerenciador de Tráfego é usado para decidir quais pontos de extremidade devem receber o tráfego.

No Serviço de Aplicativo, um aplicativo é executado em um [Plano de Serviço de Aplicativo](/azure/app-service/overview-hosting-plans). Um plano de serviço de aplicativo define um conjunto de recursos de computação para um aplicativo ser executado. Você pode gerenciar o Plano do Serviço de Aplicativo e o aplicativo Web em grupos diferentes.

O código do guia estratégico nesta seção define os seguintes recursos:

* Grupo de recursos do Azure no qual o plano do Serviço de Aplicativo é implantado
* Plano do Serviço de Aplicativo
* Grupo de recursos do Azure no qual o aplicativo é implantado
* Serviço de aplicativo no Linux com o Java 8 e o runtime do contêiner Tomcat
* Perfil do Gerenciador de Tráfego
* Ponto de extremidade do Gerenciador de Tráfego usando o aplicativo criado

Salve o guia estratégico a seguir como `webapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook webapp.yml
```

Depois de executar o guia estratégico, você verá resultados semelhantes aos seguintes:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Tutorial: Dimensionar aplicativos no Serviço de Aplicativo do Azure usando o Ansible](/azure/ansible/ansible-scale-azure-web-apps)