---
title: "设置复制策略以使用 Azure Site Recovery 将 Hyper-V VM（包含 VMM）复制到 Azure | Microsoft Docs"
description: "介绍如何设置复制策略，以使用 Azure Site Recovery 将 Hyper-V VM（包含 VMM）复制到 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ee808b20-324b-4198-b831-edb65b95e8b7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 592e1c3f647e5b1f1d9aa776657e8f89b60349e1
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="step-10-set-up-a-replication-policy-for-hyper-v-vm-replication-with-vmm-to-azure"></a>步骤 10：创建复制策略以将 Hyper-V VM（包含 VMM）复制到 Azure


设置[网络映射](vmm-to-azure-walkthrough-network-mapping.md)后，请使用本文配置一个复制策略，以在 Azure 门户中通过 [Azure Site Recovery](site-recovery-overview.md) 服务将 System Center Virtual Machine Manager (VMM) 云中托管的本地 Hyper-V 虚拟机复制到 Azure。

阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。



## <a name="create-a-policy"></a>创建策略

1. 若要创建新的复制策略，请单击“准备基础结构” > “复制设置” > “+创建和关联”。

    ![网络](./media/vmm-to-azure-walkthrough-replication/gs-replication.png)
2. 在“创建和关联策略”中指定策略名称。
3. 在“复制频率” 中，指定要在初始复制后复制增量数据的频率（每隔 30 秒、5 或 15 分钟）。

    > [!NOTE]
    >  复制到高级存储时，不支持 30 秒的频率。 该限制取决于高级存储支持的每个 blob 快照数 (100)。 [了解详细信息](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. 在“恢复点保留期”中，针对每个恢复点指定保留期的时长（以小时为单位）。 受保护的计算机可以恢复到某个时段内的任意时间点。
5. 在“应用一致性快照频率” 中，指定创建包含应用程序一致性快照的恢复点的频率（1-12 小时）。 Hyper-V 使用两种类型的快照 — 标准快照，它提供整个虚拟机的增量快照；与应用程序一致的快照，它生成虚拟机内的应用程序数据的时间点快照。 与应用程序一致的快照使用卷影复制服务 (VSS) 来确保应用程序在拍摄快照时处于一致状态。 请注意，如果启用了与应用程序一致的快照，它将影响在源虚拟机上运行的应用程序的性能。 请确保设置的值小于配置的额外恢复点的数目。
6. 在“初始复制开始时间”中，指定开始初始复制的时间。 复制通过 Internet 带宽进行，因此，可能需要将它安排在非繁忙时间。
7. 在“加密 Azure 上存储的数据”中，指定是否加密 Azure 存储中的静态数据。 然后单击“确定”。

    ![复制策略](./media/vmm-to-azure-walkthrough-replication/gs-replication2.png)
8. 当创建新策略时，该策略自动与 VMM 云关联。 单击 **“确定”**。 可以在“复制”“策略名称”>“关联 VMM 云”中，将其他 VMM 云（及其中的 VM）与此复制策略相关联。

    ![复制策略](./media/vmm-to-azure-walkthrough-replication/policy-associate.png)



## <a name="next-steps"></a>后续步骤

转到[步骤 11：启用复制](vmm-to-azure-walkthrough-enable-replication.md)

