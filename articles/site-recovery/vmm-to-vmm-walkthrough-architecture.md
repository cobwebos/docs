---
title: "查看使用 Azure Site Recovery 执行 Hyper-V 到辅助站点的复制的体系结构 | Microsoft Docs"
description: "本文概述通过 Azure Site Recovery 将本地 Hyper-V VM 复制到辅助 System Center VMM 站点所用的体系结构。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 07099161-4cc7-4f32-8eb9-2a71bbf0750b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: b78cd0d5a5395873afaddc8856004775f447e8ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-a-secondary-site"></a>步骤 1：查看从 Hyper-V 复制到辅助站点的体系结构

本文介绍如何通过 Azure 门户在 System Center Virtual Machine Manager (VMM) 云中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 Hyper-V 虚拟机 (VM) 复制到辅助 VMM 站点时涉及的组件和进程。

请将任何评论发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。



## <a name="architectural-components"></a>体系结构组件

将 Hyper-V VM 复制到辅助 VMM 站点时需要提供：

**组件** | **位置** | **详细信息**
--- | --- | ---
**Azure** | Azure 中的订阅。 | 在 Azure 订阅中创建恢复服务保管库，以便协调和管理不同 VMM 位置之间的复制。
**VMM 服务器** | 需要 VMM 主位置和辅助位置。 | 我们建议在主站点和辅助站点中各一个 VMM 服务器 
**Hyper-V 服务器** |  主要和辅助 VMM 云中需要一台或多台 Hyper-V 主机服务器。 | 使用 Kerberos 或证书身份验证通过 LAN 或 VPN 在主要和辅助 Hyper-V 主机服务器之间复制数据。  
**Hyper-V VM** | 在 Hyper-V 主机服务器上。 | 源主机服务器应该至少有一个要复制的 VM。

## <a name="replication-process"></a>复制过程

1. 设置 Azure 帐户，创建恢复服务保管库，然后指定要复制的内容。
2. 配置源和目标的复制设置，其中包括在 VMM 服务器上安装 Azure Site Recovery 提供程序，以及在每个 Hyper-V 主机上安装 Microsoft Azure 恢复服务代理。
3. 为源 VMM 云创建复制策略。 策略将应用到云中主机上的所有 VM。
4. 为每个 VMM 启用复制，并按所选设置对 VM 进行初始复制。
5. 初始复制后，开始增量更改复制。 项的受跟踪更改保存在 .hrl 文件中。


![本地到本地](./media/vmm-to-vmm-walkthrough-architecture/arch-onprem-onprem.png)

## <a name="failover-and-failback-process"></a>故障转移和故障回复过程

1. 可以在本地站点之间运行计划内或计划外[故障转移](site-recovery-failover.md)。 如果运行计划的故障转移，源 VM 将关闭以确保不会丢失数据。
2. 可以故障转移单台计算机，或创建[恢复计划](site-recovery-create-recovery-plans.md)来协调多台计算机的故障转移。
4. 如果执行了到辅助站点的计划外故障转移，在故障转移后，不会为辅助位置中的计算机启用保护或复制。 如果执行了计划内故障转移，在故障转移后，辅助位置中的计算机将受保护。
5. 然后提交故障转移，开始从副本 VM 访问工作负荷。
6. 当主站点再次可用时，启动从辅助站点到主站点的反向复制。 反向复制会使虚拟机进入受保护状态，但辅助数据中心仍是活动位置。
7. 要使主站点再次成为活动位置，可以启动从辅助站点到主站点的计划内故障转移，再次启动反向复制。



## <a name="next-steps"></a>后续步骤

转到[第 2 步：查看先决条件和限制](vmm-to-vmm-walkthrough-prerequisites.md)。
