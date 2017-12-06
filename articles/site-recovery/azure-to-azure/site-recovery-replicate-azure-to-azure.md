---
title: "使用 Azure Site Recovery 将 Azure VM 复制到次要区域 | Microsoft Docs"
description: "本文介绍如何使用 Azure Site Recovery 服务，将一个 Azure 区域中运行的 Azure VM 复制到另一个区域。"
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
ms.date: 11/29/2017
ms.author: asgang
ms.openlocfilehash: 114390712f5e5bf62ec515db62469e09361b8f85
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>将 Azure 虚拟机复制到另一个 Azure 区域


本文介绍如何使用 Azure Site Recovery 服务，将一个 Azure 区域中运行的 Azure 虚拟机 (VM) 复制到 Azure 次要区域。

>[!NOTE]
>
> 使用 Site Recovery 复制 Azure VM 的功能目前处于预览状态。

## <a name="prerequisites"></a>先决条件

* 应已部署恢复服务保管库。 我们建议在要将 VM 复制到的目标区域中创建该保管库。
* 如果使用网络安全组 (NSG) 规则或防火墙代理来控制 Azure VM 的出站互联网连接访问，请确保允许所需的 URL 或 IP。 [了解详细信息](./site-recovery-azure-to-azure-networking-guidance.md)。
* 如果在本地和 Azure 中的源位置之间建立了 ExpressRoute 或 VPN 连接，请[了解如何设置](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration)。
* Azure 用户帐户需要特定[权限](../site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)才能启用 Azure VM 的复制。
应启用 Azure 订阅，以便在用作灾难恢复区域的目标位置创建 VM。 请联系支持部门，启用所需配额。

## <a name="enable-replication"></a>启用复制

在此过程中，“东亚”用作源位置，“东南亚”用作目标。

1. 在保管库中单击“+复制”，启用对虚拟机的复制。
2. 确认“源:”设置为“Azure”。
3. 将“源位置”设置为“东亚”。
4. 在“部署模型”中，选择“经典”或“资源管理器”。
5. 在“资源组”中，选择源 VM 所属的组。 将列出所选资源组下的所有 VM。

    ![启用复制](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

6. 在“虚拟机”>“选择虚拟机”中，单击并选择要复制的每个 VM。 只能选择可以启用复制的计算机。 然后单击“确定”。

    ![启用复制](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)
    
7. 在“设置” > “目标位置”下，指定要将源 VM 复制到的位置。 Site Recovery 会根据选定 VM 所在的区域提供合适的目标区域列表。
8. Site Recovery 会设置默认目标设置。 可根据需要修改这些设置。

    - **目标资源组**。 默认情况下，Site Recovery 会在目标区域中创建一个带有“asr”后缀的新资源组。 如果创建的资源组已存在，将重复使用它。
    - **目标虚拟网络**。 默认情况下，Site Recovery 会在目标区域中创建一个带有“asr”后缀的新虚拟网络。 此网络映射到源网络。 [详细了解](site-recovery-network-mapping-azure-to-azure.md)网络映射。
    - **目标存储帐户**。 默认情况下，Site Recovery 会创建与源 VM 存储配置匹配的新目标存储帐户。 如果创建的帐户已存在，将重复使用它。
    - **缓存存储帐户**。 Azure Site Recovery 会创建附加的缓存存储帐户和源区域。 在复制到目标位置前，系统会跟踪源 VM 上发生的所有更改并发送到缓存存储帐户。
    - **可用性集**。 默认情况下，Site Recovery 会在目标区域中创建一个带有“asr”后缀的新可用性集。 如果创建的集已存在，将重复使用它。
    - **复制策略**。 Site Recovery 会定义恢复点保留期历史记录和应用一致性快照频率的设置。 默认情况下，Site Recovery 会使用恢复点保留期为 24 小时、应用一致性快照频率为 60 分钟的默认设置创建新的复制策略。

    ![启用复制](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
9. 单击“启用复制”开始保护 VM。

## <a name="customize-target-resources"></a>自定义目标资源

1. 修改以下任何目标默认值：

    - **目标资源组**。 可从订阅内目标位置中的所有资源组列表中选择资源组。
    - **目标虚拟网络**。 从目标位置的所有虚拟网络列表中选择。
    - **可用性集**只能将可用性集设置添加到源区域中某个集内的 VM。
    - **目标存储帐户**：添加可用的任何帐户。

        ![启用复制](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. 单击“创建目标资源” > “启用复制”。 在初始复制期间，VM 状态刷新可能需要一段时间。 单击“刷新”可查看最新状态。

    ![启用复制](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)
    
3. VM 受保护后，可在“复制的项”中查看 VM 的运行状况。



## <a name="next-steps"></a>后续步骤
[了解](../azure-to-azure-tutorial-dr-drill.md)如何运行测试故障转移。

