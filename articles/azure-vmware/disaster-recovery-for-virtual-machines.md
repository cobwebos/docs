---
title: 完成虚拟机的灾难恢复
description: 本文介绍如何使用 Azure VMware 解决方案完成虚拟机的灾难恢复
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 2ccb6546b9b01255e4a28aed79fd0d3ccbc4516c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579672"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>使用 Azure VMware 解决方案完成虚拟机灾难恢复

本文包含使用 VMware HCX 解决方案完成虚拟机的灾难恢复 () Vm 的过程，并使用 Azure VMware 解决方案私有云作为恢复或目标站点。

VMware HCX 提供各种操作，可提供复制策略的精细控制和粒度。 可用的操作包括：

- **反向** –发生灾难后。 Reverse 有助于使站点 B 成为受保护 VM 现在所在的源站点和站点 A。

- **暂停** –暂停与所选 VM 关联的当前复制策略。

- **Resume** -暂停与所选 VM 关联的当前复制策略。

- **删除** -删除与所选 VM 关联的当前复制策略。

- **立即同步** –超出绑定的同步源 VM 到受保护的 vm。

本指南包含以下复制方案：

- 保护 VM 或一组 Vm。

- 完成 VM 或一组 Vm 的测试恢复。

- 恢复一个或一组 VM。

- 反向保护 VM 或一组 Vm。

## <a name="protect-vms"></a>保护 VM

1. 登录到源站点上的 **VSphere 客户端** ，并访问 **HCX 插件**。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="VSphere 中的 HCX 选项" border="true":::

1. 输入 " **灾难恢复** " 区域，然后选择 " **保护 vm**"。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="VSphere 中的 HCX 选项" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. 选择源和远程站点。 在此示例中，远程站点应为 Azure VMware 解决方案私有云。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="VSphere 中的 HCX 选项" border="true":::

1. 如果需要，请选择 **默认的复制** 选项：

   - **启用压缩：** 对于低吞吐量方案，建议使用。

   - **启用静默：** 暂停 VM，以确保将一致的副本同步到远程站点。

   - **目标存储：** 受保护的 Vm 的远程数据存储，在 Azure VMware 解决方案私有云中，这应该是 vSAN 数据存储。

   - **计算容器：** 远程 vSphere 群集或资源池。

   - **目标文件夹：** 远程目标文件夹，这是可选的，如果未选择任何文件夹，则 Vm 将直接放置在选定的群集下。

   - **RPO：** 源 VM 与受保护的 VM 之间的同步间隔时间可以介于5分钟到24小时之间。

   - **快照间隔：** 快照之间的间隔。

   - **快照数：** 已配置的快照间隔内的总快照数。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="VSphere 中的 HCX 选项" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. 从列表中选择一个或多个 Vm，并根据需要配置复制选项。

   默认情况下，Vm 继承在默认复制选项中配置的全局设置策略。 对于所选 VM 中的每个网络接口，配置远程 **网络端口组** ，并选择 " **完成** " 以启动保护过程。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="VSphere 中的 HCX 选项" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. 在同一灾难恢复区域中监视每个选定 Vm 的进程。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="VSphere 中的 HCX 选项" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. 保护 VM 后，可以在 " **快照** " 选项卡中查看不同的快照。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="VSphere 中的 HCX 选项" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   黄色三角形表示快照和虚拟尚未在测试恢复操作中进行测试。

   关闭电源的 VM 与开机的 VM 之间存在重要差异。 此图显示了已开机的 VM 的同步过程。 它会启动同步过程，直到它完成第一个快照（VM 的完整副本），然后在配置的时间间隔内完成下一个快照。 对于关闭的 VM，它将同步副本，然后 VM 显示为 "不活动"，"保护" 操作显示为 "已完成"。  当 VM 开机时，它将启动到远程站点的同步过程。

## <a name="complete-a-test-recover-of-vms"></a>完成 Vm 的测试恢复

1. 在远程站点（即 Azure VMware 解决方案私有云）上登录到 **VSphere 客户端** 。 
1. 在 **HCX 插件**内的 "灾难恢复" 区域中，选择任何 VM 上的垂直省略号以显示 "操作" 菜单，然后选择 " **测试恢复 VM**"。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="VSphere 中的 HCX 选项" border="true":::

1. 选择要用于测试 VM 不同状态的测试选项和快照。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="VSphere 中的 HCX 选项" border="true":::

1. 选择 " **测试**" 后，恢复操作开始。

1. 完成后，可以在 Azure VMware 解决方案私有云 vCenter 中查看新 VM。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="VSphere 中的 HCX 选项" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. 在 VM 或其上运行的任何应用程序进行测试后，执行清除以删除测试实例。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="VSphere 中的 HCX 选项" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>恢复 Vm

1. 登录到远程站点上的 **VSphere 客户端** （Azure VMware 解决方案私有云），并访问 **HCX 插件**。

   对于恢复方案，为此示例使用的一组 Vm。

1. 从列表中选择要恢复的 VM，打开 " **操作** " 菜单，然后选择 " **恢复 vm**"。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="VSphere 中的 HCX 选项" border="true":::

1. 为每个实例配置恢复选项，然后选择 " **恢复** " 以启动恢复操作。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="VSphere 中的 HCX 选项" border="true":::

1. 完成恢复操作后，新的 Vm 会出现在远程 vCenter Server 清单中。

## <a name="complete-a-reverse-replication-on-vms"></a>在 Vm 上完成反向复制

1. 登录到 Azure VMware 解决方案私有云上的 **VSphere 客户端** ，并访问 **HCX 插件**。
   
   >[!NOTE]
   >请确保在开始反向复制之前，已关闭源站点上的原始 Vm。 如果 Vm 未关闭电源，则操作将失败。

1. 从列表中，选择要复制回源站点的 Vm，打开 " **操作** " 菜单，然后选择 " **反向**"。 
1. 选择 " **反向** " 开始复制。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="VSphere 中的 HCX 选项" border="true":::

1. 监视每个 VM 的详细信息部分。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="VSphere 中的 HCX 选项" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>灾难恢复计划自动化

VMware HCX 目前没有用于创建和自动化灾难恢复计划的内置机制。 但是，VMware HCX 提供一组 REST Api，其中包括用于灾难恢复操作的 Api。 API 规范可在 HCX Manager 中的 URL 内访问。

这些 Api 涵盖了灾难恢复中的以下操作。

- 保护

- 恢复

- 测试恢复

- 计划的恢复

- Reverse

- 查询

- 测试清理

- 暂停

- 恢复

- 删除保护

- 重新配置

下面显示了 JSON 中的恢复操作有效负载的示例。

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

通过使用这些 Api，客户可以构建一种自定义机制来自动创建和执行灾难恢复计划。
