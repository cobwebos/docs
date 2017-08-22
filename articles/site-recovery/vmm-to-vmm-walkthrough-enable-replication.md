---
title: "使用 Azure Site Recovery 实现将 Hyper-V 复制到辅助站点 | Microsoft Docs"
description: "介绍如何使用 Azure Site Recovery 实现将 Hyper-V VM 复制到辅助 System Center VMM 站点。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d8782d14-9fef-4396-8912-ff945efc851b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 6673d192dbc18bfc955d9e7e3c55893512511ffb
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="step-9-enable-replication-to-a-secondary-site-for-hyper-v-vms"></a>步骤 9：实现将 Hyper-V VM 复制到辅助站点


设置复制策略后，通过学习本文内容，使用 [Azure Site Recovery](site-recovery-overview.md) 实现将本地 Hyper-V 虚拟机 (VM) 复制到辅助 System Center Virtual Machine Manager (VMM) 站点。

阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。



## <a name="select-vms-to-replicate"></a>选择要复制的虚拟机

1. 单击“步骤 2: 复制应用程序” > “源”。 

    ![启用复制](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication1.png)

2. 在“源”中，选择 VMM 服务器和要复制的 Hyper-V 主机所在的云。 。

    ![启用复制](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication2.png)
3. 在“目标”中，确认辅助 VMM 服务器和云。
4. 在“虚拟机”中，从列表中选择要保护的 VM。

    ![启用虚拟机保护](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication5.png)

可以在“作业” > “Site Recovery 作业”中，跟踪“启用保护”操作的进度。 “最后完成保护”作业完毕后，初始复制即已完成，虚拟机可执行故障转移。

请注意：

- 还可以在 VMM 控制台中为虚拟机启用保护。 在工具栏上的虚拟机属性的“Azure Site Recovery”选项卡中单击“启用保护”。
- 启用复制后，可以在“复制的项”中查看 VM 的属性。 在“概要”仪表板中，可以看到有关 VM 复制策略及其状态的信息。 单击“属性”可查看详细信息。

## <a name="onboard-existing-vms"></a>载入现有 VM

如果在 VMM 中已有使用 Hyper-V 副本进行复制的虚拟机，可以将它们加入到 Azure Site Recovery 复制中，如下所述：

1. 确保托管现有 VM 的 Hyper-V 服务器位于主云中，托管副本虚拟机的 Hyper-V 服务器位于辅助云中。
2. 确保为主 VMM 云配置了复制策略。
3. 为主虚拟机启用复制。 Azure Site Recovery 和 VMM 将确保检测到相同的副本主机和虚拟机，并且 Azure Site Recovery 将使用指定的设置重用并重新建立复制。


## <a name="next-steps"></a>后续步骤

转到[步骤 10：运行测试故障转移](vmm-to-vmm-walkthrough-test-failover.md)。

