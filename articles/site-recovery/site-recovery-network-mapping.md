---
title: "使用 Site Recovery 规划 Hyper-V VM 复制的网络映射 | Microsoft Docs"
description: "设置网络映射，以便进行从本地数据中心到 Azure 或者到辅助站点的 Hyper-V 虚拟机复制。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: tysonn
ms.assetid: fcaa2f52-489d-4c1c-865f-9e78e000b351
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/23/2017
ms.author: raynew
ms.openlocfilehash: b1b8b1ebc013a5dfb69528f9353369e18f84e61f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="plan-network-mapping-for-hyper-v-vm-replication-with-site-recovery"></a>使用 Site Recovery 规划 Hyper-V VM 复制的网络映射



本文可帮助理解及规划使用 [Azure Site Recovery 服务](site-recovery-overview.md)将 Hyper-V VM 复制到 Azure 或辅助站点时的网络映射。

阅读本文后，欢迎在本文底部发表看法，或在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上咨询技术问题。

## <a name="network-mapping-for-replication-to-azure"></a>复制到 Azure 的网络映射

将 Hyper-V VM（在 VMM 中管理）复制到 Azure 时会用到网络映射。 网络映射在源 VMM 服务器上的 VM 网络和目标 Azure 网络之间进行映射。 映射可执行以下操作：

- **网络连接**：确保复制的 Azure VM 连接到映射的网络。 在同一网络上进行故障转移的所有计算机都可互相连接，即使这些计算机在不同的恢复计划中进行故障转移。
- **网络网关**：如果在目标 Azure 网络上设置了网络网关，VM 可连接到其他本地虚拟机。

请注意：

- 源 VMM VM 网络会映射到 Azure 虚拟网络。
- 故障转移后，源网络中的 Azure VM 会连接到映射的目标虚拟网络。
- 执行复制时，添加到源 VM 网络的新 VM 会连接到映射的 Azure 网络。
- 如果目标网络具有多个子网，并且其中一个子网与源虚拟机所在的子网同名，则在故障转移后副本虚拟机将连接到该目标子网。
- 如果没有具有匹配名称的目标子网，则虚拟机将连接到网络中的第一个子网。


## <a name="network-mapping-for-replication-to-a-secondary-datacenter"></a>复制到辅助数据中心的网络映射

将 Hyper-V VM（在 System Center Virtual Machine Manager [VMM] 中管理）复制到辅助数据中心时会用到网络映射。 网络映射在源 VMM 服务器上的 VM 网络和目标 VMM 服务器上的 VM 网络之间进行映射。 映射可执行以下操作：

- **网络连接**：在故障转移后将 VM 连接到适当的网络。 副本 VM 会连接到已映射到源网络的目标网络。
- **最佳位置**：以最佳方式在 Hyper-V 主机服务器上放置副本 VM。 副本 VM 放在可访问映射的 VM 网络的主机上。
- **无网络映射**：如果未配置网络映射，则故障转移后，副本 VM 不会连接任何 VM 网络。

请注意：

- 可以在两个 VMM 服务器上的 VM 网络之间配置网络映射；如果两个站点由同一个服务器管理，则在可以在单个 VMM 服务器上的两个对应 VM 网络之间配置网络映射。
- 在适当配置映射且启用复制后，主位置上的 VM 将连接到网络，目标位置上的副本将连接到其映射网络。
-
- 如果已经在 VMM 中正确地设置了网络，则在网络映射期间选择目标 VM 网络时，会显示使用源 VM 网络的 VMM 源云以及用于保护的目标云上的可用目标 VM 网络。
- 如果目标网络具有多个子网，并且其中一个子网与源虚拟机所在的子网同名，则在故障转移后副本虚拟机将连接到该目标子网。 如果没有具有匹配名称的目标子网，则虚拟机将连接到网络中的第一个子网。



### <a name="example"></a>示例

以下示例演示了此机制。 假设一个组织有两个经营地点：纽约和芝加哥。

**位置** | **VMM 服务器** | **VM 网络** | **映射到**
---|---|---|---
纽约 | VMM-NewYork| VMNetwork1-NewYork | 映射到 VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | 未映射
芝加哥 | VMM-Chicago| VMNetwork1-Chicago | 映射到 VMNetwork1-NewYork
 | | VMNetwork1-Chicago | 未映射

在本示例中：

- 为连接到 VMNetwork1-NewYork 的任意虚拟机创建的副本虚拟机连接 VMNetwork1-Chicago。
- 为 VMNetwork2-NewYork 或 VMNetwork2-Chicago 创建的副本虚拟机不会连接任何网络。

这是本示例组织中 VMM 云的设置方式，以及逻辑网络与云关联的方式。

#### <a name="cloud-protection-settings"></a>云保护设置

**受保护的云** | **提供保护的云** | **逻辑网络（纽约）**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>不可用</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>不可用</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

#### <a name="logical-and-vm-network-settings"></a>逻辑和 VM 网络设置

**位置** | **逻辑网络** | **关联的 VM 网络**
---|---|---
纽约 | LogicalNetwork1-NewYork | VMNetwork1-NewYork
芝加哥 | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

#### <a name="target-network-settings"></a>目标网络设置

根据这些设置，选择目标 VM 网络时，下表显示将可用的选项。

**Select** | **受保护的云** | **提供保护的云** | **可用目标网络**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | 可用
 | GoldCloud1 | GoldCloud2 | 可用
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | 不可用
 | GoldCloud1 | GoldCloud2 | 可用


如果目标网络具有多个子网，并且其中一个子网与源虚拟机所在的子网同名，则在故障转移后副本虚拟机将连接到该目标子网。 如果没有具有匹配名称的目标子网，则虚拟机将连接到网络中的第一个子网。


#### <a name="failback-behavior"></a>故障回复行为

若要了解在故障回复（反向复制）情况下会发生，假设 VMNetwork1-NewYork 已映射到 VMNetwork1-Chicago，设置如下。


**虚拟机** | **连接 VM 网络**
---|---
VM1 | VMNetwork1-Network
VM2（VM1 的副本） | VMNetwork1-Chicago

在采用这些设置的条件下，我们看看在一些可能的方案中会发生什么情况。

**方案** | **结果**
---|---
在故障转移后，VM-2 的网络属性没有发生任何变化。 | VM-1 保持连接到源网络。
在故障转移后，VM-2 的网络属性发生变化，且连接断开。 | VM-1 断开连接。
在故障转移后，VM-2 的网络属性发生变化，且连接的是 VMNetwork2-Chicago。 | 如果未映射 VMNetwork2-Chicago，VM-1 断开连接。
VMNetwork1-Chicago 的网络映射发生变化。 | VM-1 连接现映射到 VMNetwork1-Chicago 的网络。



## <a name="next-steps"></a>后续步骤

了解如何[规划网络基础结构](site-recovery-network-design.md)。
