---
title: 完成虚拟机的灾难恢复
description: 本文介绍如何使用 AVS 完成虚拟机灾难恢复
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 86f823444d4fff3edf8651f4d949c71d2c981ec7
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740546"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>使用 Azure VMWare 解决方案完成虚拟机灾难恢复

本文包含使用 VMWare 混合云扩展（HCX）解决方案完成虚拟机灾难恢复的过程，并使用 Azure VMWare 解决方案私有云作为恢复或目标站点。

VMWare HCX 提供各种操作，可提供复制策略的精细控制和粒度。 可用的操作包括：

- 反向–发生灾难后。 Reverse 有助于使站点 B 成为受保护 VM 现在所在的源站点和站点 A。

- 暂停–暂停与所选虚拟机关联的当前复制策略。

- Resume-暂停与所选虚拟机关联的当前复制策略。

- 删除-删除与所选虚拟机关联的当前复制策略。

- 立即同步–超出绑定的同步源虚拟机到受保护的 VM。

本指南包含以下复制方案：

- 保护 VM 或一组 Vm。

- 完成 VM 或一组 Vm 的测试恢复。

- 恢复一个或一组 VM。

- 反向保护 VM 或一组 Vm。

## <a name="protect-virtual-machines"></a>保护虚拟机

登录到源站点上的**VSphere 客户端**，并访问**HCX 插件**。

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="VSphere 中的 HCX 选项" border="true":::

输入 "**灾难恢复**" 区域，然后单击 "**保护 vm**"。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="选择 "保护 vm"" border="true":::

在打开的窗口中，选择源和远程站点，在这种情况下，远程站点应为 AVS 私有云。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="保护 Vm 窗口" border="true":::

如果需要，请选择默认的复制选项：

- **启用压缩：** 对于低吞吐量方案，建议使用。

- **启用静默：** 暂停 VM，以确保将一致的副本同步到远程站点。

- **目标存储：** 为受保护的 VM 选择远程数据存储。 在 AVS 私有云中，此选择应为 VSAN 数据存储。

- **计算容器：** 远程 vSphere 群集或资源池。

- **目标文件夹：** 远程目标文件夹，此设置是可选的，如果未选择任何文件夹，则 VM 会直接在所选群集下进行进度。

- **RPO：** 此值是源虚拟机和受保护的虚拟机之间的同步间隔，可以介于5分钟到24小时之间。

- **快照间隔：** 快照之间的间隔。

- **快照数：** 已配置的快照间隔内的总快照数。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="保护虚拟机选项" border="true":::

从列表中选择一个或多个虚拟机，并根据需要配置虚拟机的复制选项。

默认情况下，虚拟机将继承在默认复制选项中配置的全局设置策略。 对于所选 VM 中的每个网络接口，配置远程**网络端口组**，并选择 "**完成**" 以启动保护过程。

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="网络接口选项" border="true":::

如下图所示，你可以在同一灾难恢复区域中监视每个所选虚拟机的进程。

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="监视保护进度" border="true":::

保护 VM 后，可以在 "**快照**" 选项卡中查看不同的快照。

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="快照列表" border="true":::

黄色三角形表示快照和虚拟尚未在测试恢复操作中进行测试。

关闭电源的 VM 与开机的 VM 之间存在重要差异。
前面的屏幕截图显示了已打开的虚拟机的同步过程。 它启动了同步过程，直到它完成第一个快照（VM 的完整副本），然后在配置的时间间隔内完成下一个快照。

对于关闭的 VM，它将同步副本，然后 VM 显示为 "不活动"，"保护" 操作将显示为 "已完成"。

当虚拟机接通电源时，它将启动到远程站点的同步过程。

## <a name="complete-a-test-recover-of-virtual-machines"></a>完成虚拟机的测试恢复

登录到远程站点上的**VSphere 客户端**，它是一种 AVS 私有云。 在**HCX 插件**内的 "灾难恢复" 区域中，选择任何 VM 上的垂直省略号以显示 "操作" 菜单。 选择 "**测试恢复 VM**"。

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="选择测试恢复 VM" border="true":::

在 "新建" 窗口中，选择测试的选项。 选择要用于测试虚拟机的不同状态的快照。

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="选择快照并单击 "测试"" border="true":::

单击 "**测试**" 后，恢复操作将开始。

测试恢复操作完成后，可以在 AVS 私有云 vCenter 中检查新 VM。

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="检查恢复操作" border="true":::

最后，在 VM 上完成测试后，或在其上运行的任何应用程序执行清除以删除测试实例。

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="清理测试实例" border="true":::

## <a name="recover-virtual-machines"></a>恢复虚拟机

登录到远程站点上的**VSphere 客户端**（AVS 私有云），并访问**HCX 插件**。

对于恢复方案，为此示例使用的一组虚拟机。

从列表中选择要恢复的虚拟机，打开 "**操作**" 菜单，然后选择 "**恢复 vm**"。

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="恢复虚拟机" border="true":::

为每个实例配置恢复选项，然后单击 "**恢复**" 以启动恢复操作。

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="恢复虚拟机确认" border="true":::

完成恢复操作后，新的 Vm 会出现在远程 vCenter Server 清单中。

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>在虚拟机上完成反向复制

登录到你的 AVS 私有云上的**VSphere 客户端**，并访问**HCX 插件**。
在开始反向复制之前，必须关闭源站点上的原始虚拟机的电源。 如果虚拟机未关闭电源，则操作将失败。

从列表中选择要复制回源站点的虚拟机，打开 "**操作**" 菜单，然后选择 "**反向**"。 在弹出窗口中，单击 "**反向**" 开始复制。

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="选择 "保护操作" 下的 "反向操作"" border="true":::

可以在每个虚拟机的详细信息部分监视复制。

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="查看反向操作的结果" border="true":::

## <a name="disaster-recovery-plan-automation"></a>灾难恢复计划自动化

VMWare HCX 目前没有用于创建和自动化灾难恢复计划的内置机制。 此功能在 HCX 中不存在。 但是，它提供了一组 REST Api，包括用于灾难恢复操作的 Api。

API 规范可以在 URL 中的 HCX Manager 内访问。

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
