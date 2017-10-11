---
title: "复制应用程序（Azure 到 Azure）| Microsoft Docs"
description: "本文介绍如何在 Azure 中设置在一个 Azure 区域中运行的虚拟机到另一个区域的复制。"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 5/22/2017
ms.author: asgang
ms.openlocfilehash: f9f97cf840b722c8cfee169dd1640e0682f287ff
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>将 Azure 虚拟机复制到另一个 Azure 区域



>[!NOTE]
>
> 用于 Azure 虚拟机的 Site Recovery 复制当前处于预览状态。

本文介绍如何设置在一个 Azure 区域中运行的虚拟机到另一个 Azure 区域的复制。

## <a name="prerequisites"></a>先决条件

* 本文假设用户已了解 Site Recovery 和恢复服务保管库。 需要预先创建“恢复服务保管库”。

    >[!NOTE]
    >
    > 建议在想进行 VM 复制的位置创建“恢复服务保管库”。 例如，如果目标位置为“美国中部”，则在“美国中部”创建保管库。

* 如果使用网络安全组 (NSG) 规则或防火墙代理来控制 Azure VM 的出站互联网连接访问，请确保将所需的 URL 或 IP 加入白名单。 有关更多详细信息，请参阅[网络指南文档](./site-recovery-azure-to-azure-networking-guidance.md)。

* 如果在 Azure 中，本地和源位置之间存在 ExpressRoute 或 VPN 连接，请按照 [Azure 到本地 ExpressRoute/VPN 配置的 Site Recovery 注意事项](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration)文档操作。

* Azure 用户帐户需要具有某些[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能启用 Azure 虚拟机的复制。

* 应启用 Azure 订阅，以在想要用作灾难恢复区域的目标位置中创建 VM。 可联系支持人员启用所需的配额。

## <a name="enable-replication-from-azure-site-recovery-vault"></a>从 Azure Site Recovery 保管库启用复制
在本演示中，我们会将在“东亚”Azure 位置运行的 VM 复制到“东南亚”位置。 步骤如下：

 在保管库中单击“+复制”，启用对虚拟机的复制。

1. **源：**指计算机的起始点，在本例中为 **Azure**。

2. **源位置：**指想要在其中保护虚拟机的 Azure 区域。 在本演示中，源位置为“东亚”

3. **部署模型：**指源计算机的 Azure 部署模型。 可选择经典部署模型或 Resource Manager 部署模型，属于特定模型的计算机会在下一步中列出，以供保护。

      >[!NOTE]
      >
      > 只能在复制经典虚拟机后将其恢复为经典虚拟机。 不能将其恢复为 Resource Manager 虚拟机。

4. **资源组：**指源虚拟机所属的资源组。 所选资源组下的所有 VM 都会在下一步中列出，以供保护。

    ![启用复制](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

在“虚拟机”>“选择虚拟机”中，单击并选择要复制的每个虚拟机。 只能选择可以启用复制的计算机。 然后，单击“确定”。
    ![启用复制](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)


在“设置”部分，可配置目标站点属性

1. **目标位置：**指要在其中复制源虚拟机数据的位置。 根据所选的计算机位置，Site Recovery 会提供合适目标区域的列表。

    > [!TIP]
    > 建议将目标位置设置为与恢复服务保管库相同的值。

2. **目标资源组：**指所有复制的虚拟机所属的资源组。默认情况下，ASR 会在目标区域中创建新的资源组，其名称以“asr”作为后缀。 如果 ASR 创建的资源组已存在，则会重复使用。还可选择对其进行自定义，如以下部分所示。    
3. **目标虚拟网络：**默认情况下，ASR 会在目标区域中创建新的虚拟网络，其名称以“asr”作为后缀。 这会映射到源网络并会用于任何将来的保护。

    > [!NOTE]
    > [请查看网络详细信息](site-recovery-network-mapping-azure-to-azure.md)，了解有关网络映射的更多信息。

4. **目标存储帐户：**默认情况下，ASR 会创建模拟源 VM 存储配置的新目标存储帐户。 如果 ASR 创建的存储帐户已存在，则会重复使用。

5. **缓存存储帐户：**ASR 需要在源区域中称为缓存帐户的额外存储帐户。 在复制到目标位置前，系统会跟踪源 VM 上发生的所有更改并发送到缓存存储帐户。

6. **可用性集：**默认情况下，ASR 会在目标区域中创建新的可用集，其名称以“asr”作为后缀。 如果 ASR 创建的可用性集已存在，则会重复使用。

7.  **复制策略：**定义恢复点保留期历史记录和应用一致性快照频率的设置。 默认情况下，ASR 会使用默认的恢复点保留期设置（“24 小时”）和应用一致性快照频率设置（“60 分钟”）创建新的复制策略。

    ![启用复制](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>自定义目标资源

如果想要更改 ASR 使用的默认值，可根据需要更改这些设置。

1. **自定义：**单击以更改 ASR 使用的默认值。

2. **目标资源组：**可从订阅内目标位置存在的所有资源组列表中选择资源组。

3. **目标虚拟网络：**可找到目标位置中所有虚拟网络的列表。

4. **可用性集：**仅可向属于源区域可用性集的虚拟机添加可用性集设置。

5. **目标存储帐户：**

![启用复制](./media/site-recovery-replicate-azure-to-azure/customize.PNG) 单击“创建目标资源”并启用复制


虚拟机受到保护后，可在“复制的项”下检查 VM 运行状况的状态

>[!NOTE]
>在初始复制期间，状态可能需要一段时间才能刷新，因而在一定时间内可能无法看到进度。 可单击边栏选项卡顶部的“刷新”按钮获取最新状态。
>

![启用复制](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)


## <a name="next-steps"></a>后续步骤
- [详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。
- [详细了解](site-recovery-failover.md)不同类型的故障转移，以及如何运行它们。
- 详细了解如何[使用恢复计划](site-recovery-create-recovery-plans.md)来缩短 RTO。
- 详细了解如何在故障转移后[重新保护 Azure VM](site-recovery-how-to-reprotect.md)。
