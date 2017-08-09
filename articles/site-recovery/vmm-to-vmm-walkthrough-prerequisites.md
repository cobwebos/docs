---
title: "查看使用 Azure Site Recovery 执行 Hyper-V 到辅助 VMM 站点的复制的先决条件 | Microsoft Docs"
description: "介绍使用 Azure Site Recovery 将 Hyper-V VM 复制到辅助 VMM 站点的先决条件。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 21ff0545-8be5-4495-9804-78ab6e24add6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 7897a30bf1774609ca8e6037dabcd5fbf4151271
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="step-2-review-the-prerequisites-and-limitations-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>步骤 2：查看将 Hyper-V VM 复制到辅助 VMM 站点的先决条件和限制


查看[方案体系结构](vmm-to-vmm-walkthrough-architecture.md)后，阅读本文以确保在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 将 System Center Virtual Machine Manager (VMM) 云中托管的本地 Hyper-V 虚拟机 (VM) 复制到辅助站点时了解部署先决条件。

阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="prerequisites-and-limitations"></a>先决条件和限制

**要求** | **详细信息**
--- | ---
**Azure** | [Microsoft Azure](http://azure.microsoft.com/) 订阅。<br/><br/> 你可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。<br/><br/> [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 定价。<br/><br/> 若要查看 Site Recovery 的受支持区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”。
**VMM 服务器** | 建议在主站点和辅助站点中各提供一个 VMM 服务器。<br/><br/> 支持在单个 VMM 服务器上的云之间复制。<br/><br/> VMM 服务器应当至少运行具有最新更新的 System Center 2012 SP1。<br/><br/> VMM 服务器需要 Internet 访问权限。
**VMM 云** | 每个 VMM 服务器必须位于一个或多个云上，所有云均必须设置有 Hyper-V 容量配置文件。 <br/><br/>云必须包含一个或多个 VMM 主机组。<br/><br/> 如果只有一台 VMM 服务器，它至少需要两个云，充当主要云和辅助云。
**Hyper-V** | Hyper-V 服务器必须至少运行具有 Hyper-V 角色且安装了最新更新的 Windows Server 2012。<br/><br/> Hyper-V 服务器应包含一个或多个 VM。<br/><br/>  Hyper-V 主机服务器应位于主要和辅助 VMM 云中的主机组内。<br/><br/> 如果在 Windows Server 2012 R2 的群集中运行 Hyper-V，则安装[更新 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> 如果在 Windows Server 2012 的群集中运行 Hyper-V，并且具有基于静态 IP 地址的群集，则不会自动创建群集代理。 手动配置群集代理。 [了解详细信息](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。<br/><br/> Hyper-V 服务器需要访问Internet。




## <a name="next-steps"></a>后续步骤

转到[步骤 3：规划网络](vmm-to-vmm-walkthrough-network.md)。

