---
title: 教程 - 使用 Ansible 在 Azure 应用服务中配置应用
description: 了解如何使用 Java 8 和 Tomcat 容器运行时在 Azure 应用服务中创建应用
keywords: ansible, azure, devops, bash, playbook, Azure 应用服务, Web 应用, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 5104db6bd7fa57600c7212e041263971ca4c91d4
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242051"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>教程：使用 Ansible 在 Azure 应用服务中配置应用

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 使用 Java 8 和 Tomcat 容器运行时在 Azure 应用服务中创建应用
> * 创建 Azure 流量管理器配置文件
> * 使用所创建的应用定义流量管理器终结点

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>创建基本的应用服务

本部分中的 playbook 代码定义以下资源：

* 在其中部署应用服务计划和应用的 Azure 资源组
* 采用 Java 8 的 Linux 上的应用服务 和 Tomcat 容器运行时

将以下 playbook 保存为 `firstwebapp.yml`：

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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook firstwebapp.yml
```

运行 playbook 后，可看到类似于以下结果的输出：

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

## <a name="create-an-app-and-use-azure-traffic-manager"></a>创建应用并使用 Azure 流量管理器

可使用 [Azure 流量管理器](/azure/app-service/web-sites-traffic-manager)来控制如何将来自 Web 客户端的请求分发到 Azure 应用服务中的应用。 将应用服务终结点添加到 Azure 流量管理器配置文件时，流量管理器会跟踪应用服务应用的状态。 状态包括“正在运行”、“已停止”和“已删除”。 流量管理器用于确定应接收流量的终结点。

在应用服务中，应用在[应用服务计划](/azure/app-service/overview-hosting-plans)中运行。 应用服务计划为要运行的应用定义一组计算资源。 可以在不同的组中管理应用服务计划和 Web 应用。

本部分中的 playbook 代码定义以下资源：

* 在其中部署应用服务计划的 Azure 资源组
* 应用服务计划
* 在其中部署应用的 Azure 资源组
* 采用 Java 8 的 Linux 上的应用服务 和 Tomcat 容器运行时
* 流量管理器配置文件
* 使用创建的应用的流量管理器终结点

将以下 playbook 保存为 `webapp.yml`：

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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook webapp.yml
```

运行 playbook 后，可看到类似于以下结果的输出：

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

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [教程：使用 Ansible 在 Azure 应用服务中缩放应用](/azure/ansible/ansible-scale-azure-web-apps)