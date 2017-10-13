---
title: "创建复制策略以便使用 Azure Site Recovery 将 VMware VM 复制到 Azure | Microsoft Docs"
description: "总结了创建复制策略以便将 VMware VM 复制到 Azure 存储的必要步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d7874bd8-6626-4668-9ec9-dbd2d26f8f81
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 3c4b7ad16e6a03fb605447def18f7475d502fdd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-9-set-up-a-replication-policy-for-vmware-vm-replication-to-azure"></a>第 9 步：创建复制策略以便将 VMware VM 复制到 Azure


本文介绍了如何创建复制策略，以便在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将 VMware VM 复制到 Azure。


请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="configure-a-policy"></a>配置策略

在开始之前，获取短片概述：
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

1. 若要创建新的复制策略，请单击“Site Recovery 基础结构” > “复制策略” > “+ 复制策略”。
2. 在“创建复制策略”中指定策略名称。
3. 在“RPO 阈值”中：指定 RPO 限制。 此值指定创建数据恢复点的频率。 如果连续复制超出此限制，将生成警报。
4. 在“恢复点保留期”中，针对每个恢复点指定保留期窗口的长度（以小时为单位）。 可以将复制的虚拟机恢复到窗口中的任何点。 复制到高级存储的计算机最多支持 24 小时的保留期，复制到标准存储的计算机最多支持 72 小时的保留期。
5. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（以分钟为单位）。 单击“确定”创建该策略。

    ![复制策略](./media/vmware-walkthrough-replication/gs-replication2.png)
8. 创建新策略时，该策略会自动与配置服务器关联。 默认情况下会自动创建一个匹配策略以用于故障回复。 例如，如果复制策略是 **rep-policy**，则故障回复策略将是 **rep-policy-failback**。 从 Azure 启动故障回复之前，不会使用此策略。

## <a name="next-steps"></a>后续步骤

转到[第 10 步：安装 Mobility Service](vmware-walkthrough-install-mobility.md)
