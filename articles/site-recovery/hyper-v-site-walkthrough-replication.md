---
title: "使用 Azure Site Recovery 为将 Hyper-V VM（不含 System Center VMM）复制到 Azure 设置复制策略| Microsoft Docs"
description: "总结将 Hyper-V VM 复制到 Azure 存储时设置复制策略所需的步骤"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: ca5bec5cf1152e6259b9fe7a869edd2d62b88e1a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="step-9-set-up-a-replication-policy-for-hyper-v-vm-replication-to-azure"></a>步骤 9： 为将 Hyper-V VM 复制到 Azure 设置复制策略

本文介绍在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将 Hyper-V VM（不含 System Center VMM）复制到 Azure 时如何设置复制策略。


请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="about-snapshots"></a>关于快照

Hyper-V 使用两种类型的快照 — 标准快照，它提供整个虚拟机的增量快照；与应用程序一致的快照，它生成虚拟机内的应用程序数据的时间点快照。
    - 与应用程序一致的快照使用卷影复制服务 (VSS) 来确保应用程序在拍摄快照时处于一致状态。
    - 如果启用了与应用程序一致的快照，它将影响在源虚拟机上运行的应用程序的性能。 请确保设置的值小于配置的额外恢复点的数目。

## <a name="set-up-a-replication-policy"></a>设置复制策略

1. 若要创建新的复制策略，请单击“准备基础结构” > “复制设置” > “+创建和关联”。

    ![网络](./media/hyper-v-site-walkthrough-replication/gs-replication.png)
2. 在“创建和关联策略”中指定策略名称。
3. 在“复制频率” 中，指定要在初始复制后复制增量数据的频率（每隔 30 秒、5 或 15 分钟）。

    > [!NOTE]
    > 复制到高级存储时，不支持 30 秒的频率。 该限制取决于高级存储支持的每 blob 快照数 (100)。 [了解详细信息](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)。

4. 在“恢复点保留期”中，针对每个恢复点指定保留期窗口的长度（以小时为单位）。 VM 可以恢复到某个时段内的任意时间点。
5. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（1-12 小时）。
6. 在“初始复制开始时间”中，指定开始初始复制的时间。 复制通过 Internet 带宽进行，因此，可能需要将它安排在非繁忙时间。 。

    ![复制策略](./media/hyper-v-site-walkthrough-replication/gs-replication2.png)

当创建新策略时，该策略自动与 Hyper-V 站点关联。 可以在“复制”> 策略名称 >“关联 Hyper-V 站点”中，将一个 Hyper-V 站点（以及其中的 VM）与多个复制策略关联。



## <a name="next-steps"></a>后续步骤

转到[步骤 10：启用复制](hyper-v-site-walkthrough-enable-replication.md)
