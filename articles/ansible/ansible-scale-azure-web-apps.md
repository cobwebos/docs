---
title: 教程 - 使用 Ansible 在 Azure 应用服务中缩放应用
description: 了解如何在 Azure 应用服务中纵向扩展应用
keywords: ansible, azure, devops, bash, playbook, Azure 应用服务, Web 应用, 缩放, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155913"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>教程：使用 Ansible 在 Azure 应用服务中缩放应用

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 获取现有的应用服务计划的事实
> * 将应用服务计划纵向扩展为具有三个辅助角色的 S2

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure 应用服务应用** - 如果没有 Azure 应用服务应用，则请[使用 Ansible 在 Azure 应用服务中配置应用](ansible-create-configure-azure-web-apps.md)。

## <a name="scale-up-an-app"></a>纵向扩展应用

有两个工作流可用于缩放、纵向扩展和横向扩展   。

**纵向扩展：** 纵向扩展意味着要获得更多资源。 这些资源包括 CPU、内存、磁盘空间、VM 等。 可通过更改应用所属的应用服务计划的定价层来纵向扩展应用。 
**横向扩展：** 横向扩展意味着要增加运行应用的 VM 实例数量。 根据应用服务计划定价层，可横向扩展到多达 20 个实例。 借助[自动缩放](/azure/azure-monitor/platform/autoscale-get-started)，可以根据预定义的规则和计划自动横向扩展实例计数。

本部分中的 playbook 代码定义以下操作：

* 获取现有的应用服务计划的事实
* 将应用服务计划更新为具有三个辅助角色的 S2

将以下 playbook 保存为 `webapp_scaleup.yml`：

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
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

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook webapp_scaleup.yml
```

运行 playbook 后，可看到类似于以下结果的输出：

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [Azure 上的 Ansible](/azure/ansible/)