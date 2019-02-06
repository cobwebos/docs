---
title: 使用 Ansible 创建 Azure Web 应用
description: 了解如何使用 Ansible 在 Linux 上的应用服务中创建采用 Java 8 和 Tomcat 容器运行时的 Web 应用
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, Azure 应用服务, Web 应用, Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 106fe11355d629e57f71b12d1125d188b98c2236
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051339"
---
# <a name="create-azure-app-service-web-apps-by-using-ansible"></a>使用 Ansible 创建 Azure 应用服务 Web 应用
[Azure 应用服务 Web 应用](https://docs.microsoft.com/azure/app-service/overview)（简称 Web 应用）可托管 Web 应用程序、REST API 和移动后端。 可以使用 .NET、NET Core、Java、Ruby、Node.js、PHP 或 Python 等偏好的语言进行开发。

使用 Ansible 可以在环境中自动部署和配置资源。 本文介绍如何使用 Ansible 通过 Java 运行时创建 Web 应用。 

## <a name="prerequisites"></a>先决条件
- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> 在本教程中运行以下示例 playbook 需要 Ansible 2.7。

## <a name="create-a-simple-app-service"></a>创建一个简单的应用服务
本部分提供一个示例 Ansible playbook，它定义了以下资源：
- 你的应用服务计划和 Web 应用将部署到其中的资源组
- Linux 上的应用服务中采用 Java 8 和 Tomcat 容器运行时的 Web 应用

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
将上面的 playbook 另存为 **firstwebapp.yml**。

若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook firstwebapp.yml
```

运行 Ansible playbook 得到的输出显示 Web 应用已成功创建：

```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] *************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP *************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-service-by-using-traffic-manager"></a>使用流量管理器创建应用服务
可以使用 [Azure 流量管理器](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager)来控制如何将来自 Web 客户端的请求分发到 Azure 应用服务中的应用。 将应用服务终结点添加到 Azure 流量管理器配置文件时，流量管理器会跟踪应用服务应用的状态。 状态包括“正在运行”、“已停止”和“已删除”。 然后，流量管理器就可以确定这些终结点中的哪一个应该接收流量。

在应用服务中，应用在[应用服务计划](https://docs.microsoft.com/azure/app-service/overview-hosting-plans
)中运行。 应用服务计划为要运行的 Web 应用定义一组计算资源。 可以在不同的组中管理应用服务计划和 Web 应用。

本部分提供一个示例 Ansible playbook，它定义了以下资源：
- 你的应用服务计划将部署到其中的资源组
- 应用服务计划
- 另一个资源组，你的 Web 应用将部署到其中
- Linux 上的应用服务中采用 Java 8 和 Tomcat 容器运行时的 Web 应用
- 流量管理器配置文件
- 流量管理器终结点，使用所创建的网站

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
将上一 playbook 另存为 **webapp.yml**，或者[下载 playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml)。

若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook webapp.yml
```

运行 Ansible playbook 得到的输出显示应用服务计划、Web 应用、流量管理器配置文件和终结点已成功创建：
```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [使用 Ansible 缩放 Azure 应用服务 Web 应用](https://docs.microsoft.com/azure/ansible/ansible-scale-azure-web-apps)