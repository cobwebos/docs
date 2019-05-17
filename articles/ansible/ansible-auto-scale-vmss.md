---
title: 教程 - 使用 Ansible 在 Azure 中自动缩放虚拟机规模集 | Microsoft Docs
description: 了解如何使用 Ansible 在 Azure 中通过自动缩放来缩放虚拟机规模集
keywords: ansible, azure, devops, bash, playbook, 缩放, 自动缩放, 虚拟机, 虚拟机规模集, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 4f2cd66b7460fc6fe48cb55f45bf4bc309ae054c
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231282"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>教程：使用 Ansible 在 Azure 中自动缩放虚拟机规模集

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

自动调整 VM 实例数量的功能被称为[自动缩放](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)。 自动缩放的优势在于减少监视和优化应用程序性能所产生的管理开销。 自动缩放可根据需求或规定计划进行配置。 通过 Ansible 可指定自动缩放规则，用于定义提供积极客户体验而可接受的性能。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 定义自动缩放配置文件
> * 按定期计划自动缩放
> * 按应用性能自动缩放
> * 检索自动缩放设置信息 
> * 禁用自动缩放设置

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>按计划自动缩放

若要在规模集上启用自动缩放，首先要定义自动缩放配置文件。 此配置文件定义默认、最小和最大规模集容量。 这些限制可让你通过不继续创建 VM 实例来控制成本，并可使用缩小事件中保留的最小数量的实例均衡可接受的性能。 

通过 Ansible 可按特定日期或定期计划缩放规模集。

本部分的 playbook 代码在每周一上午 10 点将 VM 实例数增加到 3 个。

将以下 playbook 保存为 `vmss-auto-scale.yml`：

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>按性能数据自动缩放

如果应用程序需求提高，规模集中 VM 实例上的负载将会增大。 如果这种负载增大持续稳定，而不只是短暂的需求，那么可以配置自动缩放规则来增加规模集中的 VM 实例数。 创建这些 VM 实例并部署应用程序后，规模集会开始通过负载均衡器将流量分配到这些实例和应用程序。 通过 Ansible 可控制 CPU 使用情况、磁盘使用情况和应用加载时间等要监视的指标。 可根据性能指标阈值，按定期计划或特定日期缩放和扩展规模集。 

本部分的 playbook 代码在每周一下午 6 点检查过去 10 分钟的 CPU 工作负载。 

playbook 根据 CPU 百分比指标执行以下操作之一：

- 将 VM 实例数扩展为 4 个
- 将 VM 实例数缩减为 1 个

将以下 playbook 保存为 `vmss-auto-scale-metrics.yml`：

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>获取自动缩放设置信息 

本部分的 playbook 代码使用 `azure_rm_autoscale_facts` 模块来检索自动缩放设置的详细信息。

将以下 playbook 保存为 `vmss-auto-scale-get-settings.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>禁用自动缩放设置

有两种方法可禁用自动缩放设置。 一种方法是将 `enabled` 密钥从 `true` 更改为 `false`。 另一种方法是删除该设置。

本部分的 playbook 代码将删除自动缩放设置。 

将以下 playbook 保存为 `vmss-auto-scale-delete-setting.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [教程：使用 Ansible 更新 Azure 虚拟机规模集的自定义映像](./ansible-vmss-update-image.md)