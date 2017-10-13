---
title: "查看体系结构以便将 VMware VM 复制到 Azure | Microsoft Docs"
description: "本文概述了使用 Azure Site Recovery 服务将本地 VMware VM 复制到 Azure 所需使用的组件和体系结构"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27.017
ms.author: raynew
ms.openlocfilehash: 2bbab5f1ac0efe9632ad6c818504584e2503cf15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-vmware-replication-to-azure"></a>第 1 步：查看体系结构以便将 VMware VM 复制到 Azure

本文介绍了使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 VMware 虚拟机复制到 Azure 所需使用的组件和进程。

请将任何评论发布到本文底部，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提问。


## <a name="architectural-components"></a>体系结构组件

下表汇总了所需的组件。

**组件** | **要求** | **详细信息**
--- | --- | ---
**Azure** | 需要创建 Azure 订阅、Azure 存储帐户和 Azure 网络。 | 复制的数据存储在存储帐户中。 从本地站点运行故障转移时，使用复制的数据创建 Azure VM。 创建 Azure VM 后，它们将连接到 Azure 虚拟网络。
**配置服务器** | 一个运行所有用于部署的本地 Site Recovery 组件的本地管理服务器 (VMware VM)。 具体包括配置服务器、进程服务器、master 目标服务器。 | 配置服务器组件在本地和 Azure 之间协调通信并管理数据复制。
 **进程服务器**：  | 默认安装在配置服务器上。 | 充当复制网关。 接收复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储。<br/><br/> 进程服务器还处理用于保护计算机的移动服务的推送安装，并执行 VMware VM 的自动发现。<br/><br/> 随着部署扩大，可以添加作为进程服务器运行的独立专用进程服务器，目的仅在于处理更大的复制流量。
 **主目标服务器** | 默认在本地配置服务器上安装。 | 处理从 Azure 进行故障回复期间产生的复制数据。<br/><br/> 如果故障回复流量很大，可以单独部署一台主目标服务器用于故障回复。
**VMware 服务器** | VMware VM 托管在 vSphere ESXi 服务器上，我们建议使用 vCenter 服务器来管理主机。 | 可将 VMware 服务器添加到恢复服务保管库。
**复制的计算机** | Mobility Service 安装在复制的每个 VMware VM 上。 可在每台计算机上手动安装，或者通过进程服务器进行推送安装。

**图 1：VMware 到 Azure 的组件**

![组件](./media/vmware-walkthrough-architecture/arch-enhanced.png)

## <a name="replication-process"></a>复制过程

1. 创建部署，包括本地和 Azure 组件。 在恢复服务保管库中，指定复制源和目标，创建配置服务器，创建复制策略，部署 Mobility Service，启用复制，运行测试故障转移。
2. 计算机根据复制策略进行复制，初始数据副本将复制到 Azure 存储中。
3. 完成初始复制后，开始将增量更改复制到 Azure。 计算机的受跟踪更改保存在 .hrl 文件中。
    - 复制计算机与 HTTPS 443 入站端口上的配置服务器通信，进行复制管理。
    - 通过复制计算机，可以将复制数据发送到 HTTPS 9443 入站端口（可修改）上的进程服务器。
    - 配置服务器通过 HTTPS 443 出站端口来与 Azure 协调复制管理。
    - 进程服务器从源计算机接收数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。
    - 如果启用了多 VM 一致性，复制组中的计算机将通过端口 20004 相互通信。 如果将多台计算机分组到复制组，并且这些组在故障转移时共享崩溃一致且应用一致的恢复点，请使用多 VM 方案。 如果计算机运行相同的工作负荷并需要保持一致，这种做法非常有用。
4. 流量通过 Internet 复制到 Azure 存储公共终结点。 或者，可以使用 Azure ExpressRoute [公共对等互连](../expressroute/expressroute-circuit-peerings.md#azure-public-peering)。 不支持通过站点到站点 VPN 将流量从本地站点复制到 Azure。


**图 2：VMware 到 Azure 的复制**

![增强版](./media/vmware-walkthrough-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>故障转移和故障回复过程

1. 在验证故障转移可以正常工作以后，即可根据需要运行到 Azure 的计划外故障转移。 不支持计划内故障转移。
2. 可以故障转移单台计算机，或创建[恢复计划](site-recovery-create-recovery-plans.md)来故障转移多台 VM。
3. 运行故障转移时，会在 Azure 中创建副本 VM。 提交故障转移，开始从副本 Azure VM 访问工作负荷。
4. 当本地主站点再次可用时，便可以故障回复。 设置故障回复基础结构，开始将计算机从辅助站点复制到主站点，然后从辅助站点运行计划外故障转移。 提交此故障转移后，数据将回到本地，此时需要再次启用目标为 Azure 的复制。 [了解详细信息](site-recovery-failback-azure-to-vmware.md)

故障回复有以下几项要求：


- **Azure 中的临时进程服务器**：如果要在故障转移后从 Azure 故障回复，必须将 Azure VM 设置为进程服务器，以处理来自 Azure 的复制。 故障回复完成后，可以删除此 VM。
- **VPN 连接**：若要进行故障回复，需要设置从 Azure 网络到本地站点的 VPN 连接（或 Azure ExpressRoute）。
- **单独的本地主目标服务器**：本地主目标服务器用于处理故障回复。 主目标服务器默认安装在管理服务器上，但如果要故障回复大量数据，应该设置独立的本地主目标服务器。
- **故障回复策略**：若要复制回到本地站点，需要创建故障回复策略。 创建复制策略已自动创建故障回复策略。
- VMware 基础结构：须故障回复到本地 VMware VM。 这意味着，即使将本地物理服务器复制到 Azure，也需要本地 VMware 基础结构。

**图 3：VMware/物理机故障回复**

![故障回复](./media/vmware-walkthrough-architecture/enhanced-failback.png)


## <a name="next-steps"></a>后续步骤

转到[第 2 步：验证先决条件和限制](vmware-walkthrough-prerequisites.md)
