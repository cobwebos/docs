---
title: 使用 Ansible 在 Azure 中自动缩放虚拟机规模集
description: 了解如何在 Azure 中使用 Ansible 通过自动缩放来缩放虚拟机规模集
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, 缩放, 自动缩放, 虚拟机, 虚拟机规模集, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: 7721dba37131616122f8a5a902e3c63de5c7157f
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157048"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>使用 Ansible 在 Azure 中自动缩放虚拟机规模集
使用 Ansible 可以在环境中自动部署和配置资源。 可以在 Azure 中使用 Ansible 管理虚拟机规模集 (VMSS)，就像管理任何其他 Azure 资源一样。 

创建规模集时，可定义想运行的 VM 实例数。 若应用程序需要更改，可自动增加或减少 VM 实例数。 通过自动缩放功能，可随客户需求的改变而进行调整，或在应用的整个生命周期内响应应用程序性能更改。 本文介绍如何创建自动缩放设置并将其关联到现有的虚拟机规模集。 在自动缩放设置中，可以配置一项规则，根据需要进行横向的缩放。

## <a name="prerequisites"></a>先决条件
- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- 现有的 Azure 虚拟机规模集。 - 如果没有虚拟机规模集，请[在 Azure 中使用 Ansible 创建虚拟机规模集](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss)。

> [!Note]
> 在本教程中运行以下示例 playbook 需要 Ansible 2.7。 

## <a name="auto-scale-based-on-a-schedule"></a>按计划自动缩放   
若要在规模集上启用自动缩放，首先要定义自动缩放配置文件。 此配置文件定义默认、最小和最大规模集容量。 这些限制可让你通过不继续创建 VM 实例来控制成本，并可使用缩小事件中保留的最小数量的实例均衡可接受的性能。 

可以按定期计划或特定日期缩放虚拟机规模集。 此部分介绍一个示例 Ansible playbook，它可以创建一项自动缩放设置，在每个星期一的 10:00（太平洋时区）将规模集中的 VM 实例数增加到 3。 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
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

将此 playbook 另存为 *vmss-auto-scale.yml*。 若要运行 Ansible playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>按性能数据自动缩放
如果应用程序需求提高，规模集中 VM 实例上的负载将会增大。 如果这种负载增大持续稳定，而不只是短暂的需求，那么可以配置自动缩放规则来增加规模集中的 VM 实例数。 创建这些 VM 实例及部署应用程序后，规模集会开始通过负载均衡器将流量分配到这些实例和应用程序。 可以控制要监视的指标（例如 CPU 或磁盘）、应用程序负载必须处于给定阈值内的时间，以及要添加到规模集的 VM 实例数。

可以根据性能指标阈值，按定期计划或特定日期缩放虚拟机规模集。 此部分介绍一个示例 Ansible playbook，它在每个星期一的 18:00（太平洋时区）检查过去 10 分钟的工作负荷，将规模集中的 VM 实例数扩大到 4 或者缩减到 1，具体取决于 CPU 百分比指标。 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
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

将此 playbook 另存为 *vmss-auto-scale-metrics.yml*。 若要运行 Ansible playbook，请使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>获取现有的自动缩放设置的信息
可以使用 playbook 通过 *azure_rm_autoscale_facts* 模块获取自动缩放设置的详细信息，如下所示：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>禁用自动缩放设置
可以禁用自动缩放设置，方法是使用 playbook 将 `enabled: true` 更改为 `enabled: false` 或删除自动缩放设置，如下所示：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [适用于虚拟机规模集的 Ansible 示例 playbook](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)