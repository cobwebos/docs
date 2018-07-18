---
title: 使用 Azure Site Recovery 执行 Hyper-V 到辅助站点的复制的体系结构 | Microsoft Docs
description: 本文概述通过 Azure Site Recovery 将本地 Hyper-V VM 复制到辅助 System Center VMM 站点所用的体系结构。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: 39a397edd17327a91882535fbd00222a4ae4dddc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894290"
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>从 Hyper-V 复制到辅助站点

本文介绍如何通过 Azure 门户在 System Center Virtual Machine Manager (VMM) 云中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 Hyper-V 虚拟机 (VM) 复制到辅助 VMM 站点时涉及的组件和进程。


## <a name="architectural-components"></a>体系结构组件

下面的表和图提供了用于将 Hyper-V 复制到辅助站点的组件的概要视图。

**组件** | **要求** | **详细信息**
--- | --- | ---
**Azure** | Azure 订阅 | 在 Azure 订阅中创建恢复服务保管库，以便协调和管理不同 VMM 位置之间的复制。
**VMM 服务器** | 需要 VMM 主位置和辅助位置。 | 我们建议在主站点和辅助站点中各提供一个 VMM 服务器。
**Hyper-V 服务器** |  主要和辅助 VMM 云中需要一台或多台 Hyper-V 主机服务器。 | 使用 Kerberos 或证书身份验证通过 LAN 或 VPN 在主要和辅助 Hyper-V 主机服务器之间复制数据。  
**Hyper-V VM** | 在 Hyper-V 主机服务器上。 | 源主机服务器应该至少有一个要复制的 VM。

**本地到本地的体系结构**

![本地到本地](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>复制过程

1. 当触发初始复制时，系统会拍摄一个 [Hyper-V VM 快照](https://technet.microsoft.com/library/dd560637.aspx)。
2. VM 上的虚拟硬盘会逐一复制到辅助位置。
3. 如果在初始复制期间发生磁盘更改， 
4. 当初始复制完成时，增量复制将开始。 Hyper-V 副本复制跟踪器将跟踪这些更改，并将其记录在 Hyper-V 复制日志 (.hrl) 中。 这些日志文件位于与磁盘相同的文件夹中。 每个磁盘都有一个关联的 .hrl 文件，该文件将发送到辅助位置。 当初始复制正在进行时，快照和日志将占用磁盘资源。
5. 日志中的增量磁盘更改会进行同步，并合并到父磁盘中。


## <a name="failover-and-failback-process"></a>故障转移和故障回复过程

- 可以故障转移单个虚拟机，或者创建恢复计划来协调多个虚拟机的故障转移。
- 可以在本地站点之间运行计划内或计划外故障转移。 如果运行计划的故障转移，源 VM 将关闭以确保不会丢失数据。
    - 如果执行了到辅助站点的计划外故障转移，在故障转移后辅助位置中的虚拟机将不受保护。
    - 如果执行了计划内故障转移，在故障转移后，辅助位置中的计算机将受保护。
- 在初始故障转移运行后，可提交它来开始访问副本 VM 中的工作负载。
- 当主位置再次可用时，可以进行故障回复。
    - 启动反向复制以开始从辅助站点到主站点的复制。 反向复制会使虚拟机进入受保护状态，但辅助数据中心仍是活动位置。
    - 要使主站点再次成为活动位置，可以启动从辅助站点到主站点的计划内故障转移，再次启动反向复制。



## <a name="next-steps"></a>后续步骤


按照[此教程](hyper-v-vmm-disaster-recovery.md)启用 VMM 云之间的 Hyper-V 复制。
