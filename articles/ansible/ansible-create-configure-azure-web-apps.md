---
title: 使用 Ansible（预览版）创建 Azure Web 应用
description: 了解如何使用 Ansible 在 Linux 上的应用服务中创建采用 Java 8 和 Tomcat 容器运行时的 Web 应用。
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, Azure 应用服务, Web 应用, Java
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 1899b1fc1e0a38d859fb3a7ce2153585579650f3
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586633"
---
# <a name="create-azure-app-service-web-apps-using-ansible-preview"></a>使用 Ansible（预览版）创建 Azure 应用服务 Web 应用
[Azure 应用服务 Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-overview)（简称 Web 应用）是用于托管 Web 应用程序、REST API 和移动后端的服务。 可以使用 .NET、NET Core、Java、Ruby、Node.js、PHP 或 Python 等偏好的语言进行开发。

使用 Ansible 可以在环境中自动部署和配置资源。 本文介绍了如何使用 Ansible 创建采用 Java 运行时的 Web 应用。 

## <a name="prerequisites"></a>先决条件
- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> 在本教程中运行以下示例 playbook 需要 Ansible 2.7。 可以通过运行 `sudo pip install ansible[azure]==2.7.0rc2` 来安装 Ansible 2.7 RC 版本。 Ansible 2.7 将在 2018 年 10 月发布。 在那之后，你不需要在此指定版本，因为默认版本将是 2.7。 

## <a name="create-a-simple-app-service"></a>创建一个简单的应用服务
本部分提供一个示例 Ansible playbook，它定义了以下资源：
- 你的应用服务计划和 Web 应用将部署到其中的资源组
- Web 应用 - Linux 上的应用服务中采用 Java 8 和 Tomcat 容器运行时的 Web 应用

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myfirstResourceGroup
    webapp_name: myfirstWebApp
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
          name: myappplan
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
将上述 playbook 保存为 firstwebapp.yml。

若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook firstwebapp.yml
```

运行 Ansible playbook 得到的输出显示 Web 应用已成功创建：

```
TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ******************************
 [WARNING]: Azure API profile latest does not define an entry for
WebSiteManagementClient
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0   
```

## <a name="create-app-service-with-traffic-manager"></a>使用流量管理器创建应用服务
可以使用 [Azure 流量管理器](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager)来控制如何将来自 Web 客户端的请求分发到 Azure 应用服务中的应用。 将应用服务终结点添加到 Azure 流量管理器配置文件时，Azure 流量管理器会跟踪应用服务的状态（正在运行、已停止或已删除），以便确定那些终结点中有哪些应该接收流量。

在应用服务中，应用在应用服务计划中运行。[](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
) 应用服务计划为要运行的 Web 应用定义一组计算资源。 可以在不同的组中管理你的应用服务计划和 Web 应用。

本部分提供一个示例 Ansible playbook，它定义了以下资源：
- 你的应用服务计划将部署到其中的资源组
- 应用服务计划
- 你的 Web 应用将部署到其中的另一个资源组
- Web 应用 - Linux 上的应用服务中采用 Java 8 和 Tomcat 容器运行时的 Web 应用
- 流量管理器配置文件
- 流量管理器终结点，使用所创建的网站

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_resource_group: planResourceGroup
    app_name: myLinuxWebApp
    location: eastus
    linux_plan_name: myAppServicePlan
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ plan_resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ plan_resource_group }}"
      name: "{{ linux_plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ app_name }}"
        plan:
          resource_group: "{{ plan_resource_group }}"
          name: "{{ linux_plan_name }}"
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
      resource_group: "{{ resource_group }}"
      name: "{{ app_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group }}"
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
      resource_group: "{{ resource_group }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"

```
将 playbook 保存为 webapp.yml，或者[下载 playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml)。

若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook webapp.yml
```

运行 Ansible playbook 得到的输出显示应用服务计划、Web 应用、流量管理器配置文件和终结点已成功创建：
```
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
> [Azure 上的 Ansible](https://docs.microsoft.com/azure/ansible/)