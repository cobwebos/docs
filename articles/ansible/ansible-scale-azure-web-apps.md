---
title: 使用 Ansible 缩放 Azure 应用服务 Web 应用
description: 了解如何使用 Ansible 在 Linux 上的应用服务中创建采用 Java 8 和 Tomcat 容器运行时的 Web 应用
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, Azure 应用服务, Web 应用, 缩放, Java
author: tomarcher
manager: jeconnoc
ms.author: kyliel
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 4ec5a0691ce73a2ffebe07b316ce5b1dde8c2b49
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249373"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>使用 Ansible 缩放 Azure 应用服务 Web 应用
[Azure 应用服务 Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-overview)（简称 Web 应用）可托管 Web 应用程序、REST API 和移动后端。 可以使用 .NET、NET Core、Java、Ruby、Node.js、PHP 或 Python 等偏好的语言进行开发。

使用 Ansible 可以在环境中自动部署和配置资源。 本文介绍了如何使用 Ansible 在 Azure 应用服务中缩放应用。

## <a name="prerequisites"></a>先决条件
- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Azure 应用服务 Web 应用** - 如果还没有 Azure 应用服务 Web 应用，则可以[使用 Ansible 创建 Azure Web 应用](ansible-create-configure-azure-web-apps.md)。

## <a name="scale-up-an-app-in-app-service"></a>纵向扩展应用服务中的应用
可以通过更改应用所属的应用服务计划的定价层来进行纵向扩展。 本部分提供了一个示例 Ansible playbook，它定义了以下操作：
- 获取现有的应用服务计划的事实
- 将应用服务计划更新为具有三个辅助角色的 S2

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App serivce plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

将此 playbook 保存为 *webapp_scaleup.yml*。

若要运行此 playbook，请使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook webapp_scaleup.yml
```

运行 playbook 后，类似于以下示例的输出显示应用服务计划已成功更新为具有三个辅助角色的 S2：
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App serivce plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [Azure 上的 Ansible](https://docs.microsoft.com/azure/ansible/)