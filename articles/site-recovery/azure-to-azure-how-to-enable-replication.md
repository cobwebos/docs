---
title: "在 Azure Site Recovery 中配置 Azure VM 复制 | Microsoft Docs"
description: "本文介绍如何使用 Site Recovery 配置从一个 Azure 区域到另一个区域的 Azure VM 复制。"
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2017
ms.author: asgang
ms.openlocfilehash: 5a0e1f638d68131c3c9f1cac36d63795b87e8f41
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2018
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>将 Azure 虚拟机复制到另一个 Azure 区域


>[!NOTE]
>
> 用于 Azure 虚拟机的 Site Recovery 复制当前处于预览状态。

本文介绍如何启用从一个 Azure 区域到另一个区域的 Azure VM 复制。

## <a name="prerequisites"></a>先决条件

本文假设已根据 [Azure 到 Azure 教程](azure-to-azure-tutorial-enable-replication.md)中所述，为此方案设置了 Site Recovery。 确保满足先决条件，并创建了恢复服务保管库。



## <a name="enable-replication"></a>启用复制

启用复制。 此过程假设主要 Azure 区域是东亚，次要区域是东南亚。

1. 在保管库中，单击“+复制”。
2. 指定以下字段：
    - **源**：VM 的起始点，在本例中为 **Azure**。
    - **源位置**：要在其中保护虚拟机的 Azure 区域。 在本演示中，源位置为“东亚”
    - **部署模型**：源计算机的 Azure 部署模型。
    - **资源组**：源虚拟机所属的资源组。 所选资源组下的所有 VM 都会在下一步中列出，以供保护。

    ![启用复制](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. 在“虚拟机”>“选择虚拟机”中，单击并选择要复制的每个 VM。 只能选择可以启用复制的计算机。 然后单击“确定”。
    ![启用复制](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. 在“设置”中，可以选择性地配置目标站点设置：

    - **目标位置**：要在其中复制源虚拟机数据的位置。 根据所选的计算机位置，Site Recovery 会提供合适目标区域的列表。 我们建议将目标位置与恢复服务保管库位置保持相同。
    - **目标资源组**：所有复制虚拟机所属的资源组。 默认情况下，Azure Site Recovery 会在目标位置中创建一个名称带有“asr”后缀的新资源组。 如果 Azure Site recovery 创建的资源组已存在，则会重复使用。 还可以选择对其进行自定义，如以下部分中所示。
    - **目标虚拟网络**：默认情况下，Site Recovery 会在目标区域中创建一个名称带有“asr”后缀的新虚拟网络。 这会映射到源网络并会用于任何将来的保护。 [详细了解](site-recovery-network-mapping-azure-to-azure.md)网络映射。
    - **目标存储帐户**：默认情况下，Site Recovery 会创建模拟源 VM 存储配置的新目标存储帐户。 如果存储帐户已存在，则重复使用。
    - **缓存存储帐户**：Site Recovery 需要源区域中称为“缓存存储”的额外存储帐户。 在复制到目标位置前，系统会跟踪源 VM 上发生的所有更改并发送到缓存存储帐户。
    - **可用性集**：默认情况下，Azure Site Recovery 会在目标区域中创建一个名称带有“asr”后缀的新可用性集。 如果 Azure Site Recovery 创建的可用性集已存在，则会重复使用。
    - **复制策略**：定义恢复点保留期历史记录和应用一致性快照频率的设置。 默认情况下，Azure Site Recovery 会使用恢复点保留期为“24 小时”、应用一致性快照频率为“60 分钟”的默认设置创建新的复制策略。

    ![启用复制](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>自定义目标资源

可以修改 Site Recovery 使用的默认目标设置。

1. 单击“自定义:”修改默认设置：
    - 在“目标资源组”中，从订阅内目标位置中的所有资源组列表中选择资源组。
    - 在“目标虚拟网络”中，从目标位置中所有虚拟网络列表中选择网络。
    - 在“可用性集”中，可将可用性集设置添加到 VM（如果它们是源区域中可用性集的一部分）。
    - 在“目标存储帐户”中，选择要使用的帐户。

        ![启用复制](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. 单击“创建目标资源” > “启用复制”。
3. 为 VM 启用复制后，可以在“复制的项”下检查 VM 的运行状况

>[!NOTE]
>在初始复制期间，VM 状态刷新可能需要一段时间，但不显示进度。 单击“刷新”按钮可获取最新状态。
>

# <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。

