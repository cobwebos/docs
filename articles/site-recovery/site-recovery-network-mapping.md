---
title: "准备网络映射，以便在 Azure Site Recovery 中使用 VMM 保护 Hyper-V 虚拟机 | Microsoft 文档"
description: "设置网络映射，以便进行从本地数据中心到 Azure 或者到辅助站点的 Hyper-V 虚拟机复制。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: a798580b-b166-426b-bdca-573167b0e451
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/05/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8023643345812a9bc18dcca43cfc0ecff31a5007


---
# <a name="prepare-network-mapping-for-hyper-v-virtual-machine-protection-with-vmm-in-azure-site-recovery"></a>准备网络映射，以便在 Azure Site Recovery 中通过 VMM 进行 Hyper-V 虚拟机保护
Azure Site Recovery 有助于业务连续性和灾难恢复 (BCDR) 策略，因为它可以协调虚拟机和物理服务器的复制、故障转移和恢复。

本文介绍网络映射，你可以通过网络映射来优化网络设置的配置，以便使用 Site Recovery 在两个本地数据中心之间或本地数据中心与 Azure 之间复制位于 VMM 云中的 Hyper-V 虚拟机。 请注意，如果你是在没有 VMM 云的情况下复制 Hyper-V VM，或者是复制 VMware VM 或物理服务器，则本文与之不相关。

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="overview"></a>概述
通过部署 Azure Site Recovery 将 Hyper-V 虚拟机复制到 Azure 或辅助数据中心时，可通过 Hyper-V 副本或 SAN 复制使用网络映射。

* **在两个本地数据中心之间复制 VMM 云中的 Hyper-V 虚拟机** - 网络映射功能在源和目标 VMM 服务器上的 VM 网络之间进行映射，以便执行以下操作：
  
  * **在故障转移后连接虚拟机** - 确保在故障转移后，虚拟机会连接适当的网络。 副本虚拟机将连接到已映射到源网络的目标网络。
  * **将副本虚拟机放置在主机服务器上** - 以最佳方式在 Hyper-V 主机服务器上放置副本虚拟机。 副本虚拟机将放置在可访问映射 VM 网络的主机上。
  * **无网络映射** - 如果未配置网络映射，则复制的虚拟机在故障转移后不会连接任何 VM 网络。
* **将本地 VMM 云中的 Hyper-V 虚拟机复制到 Azure** - 网络映射功能在源 VMM 服务器上的 VM 网络与目标 Azure 网络之间进行映射，以便执行以下操作：
  
  * **在故障转移后连接虚拟机** - 在同一网络上进行故障转移的所有计算机都可以彼此互联，无论属于哪个恢复计划。
  * **网关** - 如果在目标 Azure 网络上设置了网关，虚拟机可以连接其他本地虚拟机。
  * **无网络映射** - 如果未配置网络映射，只有在同一恢复计划中进行故障转移的虚拟机才能在故障转移到 Azure 后彼此互联。

## <a name="network-mapping-example"></a>网络映射示例
可以在两个 VMM 服务器上的 VM 网络之间配置网络映射；如果两个站点由同一个服务器管理，则在可以在单个 VMM 服务器上的两个对应 VM 网络之间配置网络映射。 在正确地配置映射且启用复制后，位于主位置的虚拟机将连接到网络，其位于目标位置的副本将连接到其映射网络。

如果已经在 VMM 中正确地设置了网络，则当你在网络映射期间选择目标 VM 网络时，将显示使用源 VM 网络的 VMM 源云以及用于保护的目标云上的可用目标 VM 网络。

以下示例演示了此机制。 假设一个组织有两个经营地点：纽约和芝加哥。

| **位置** | **VMM 服务器** | **VM 网络** | **映射到** |
| --- | --- | --- | --- |
| 纽约 |VMM-NewYork |VMNetwork1-NewYork |映射到 VMNetwork1-Chicago |
| VMNetwork2-NewYork |未映射 | | |
| 芝加哥 |VMM-Chicago |VMNetwork1-Chicago |映射到 VMNetwork1-NewYork |
| VMNetwork2-Chicago |未映射 | | |

在此示例中：

* 为连接到 VMNetwork1-NewYork 的任意虚拟机创建的副本虚拟机连接 VMNetwork1-Chicago。
* 为 VMNetwork2-NewYork 或 VMNetwork2-Chicago 创建的副本虚拟机不会连接任何网络。

这是本示例组织中 VMM 云的设置方式，以及逻辑网络与云关联的方式。

### <a name="cloud-protection-settings"></a>云保护设置
| **受保护的云** | **提供保护的云** | **逻辑网络（纽约）** |
| --- | --- | --- |
| GoldCloud1 |GoldCloud2 | |
| SilverCloud1 |SilverCloud2 | |
| GoldCloud2 |<p>不可用</p><p></p> |<p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p> |
| SilverCloud2 |<p>不可用</p><p></p> |<p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p> |

### <a name="logical-and-vm-network-settings"></a>逻辑和 VM 网络设置
| **位置** | **逻辑网络** | **关联的 VM 网络** |
| --- | --- | --- |
| 纽约 |LogicalNetwork1-NewYork |VMNetwork1-NewYork |
| 芝加哥 |LogicalNetwork1-Chicago |VMNetwork1-Chicago |
| LogicalNetwork2Chicago |VMNetwork2-Chicago | |

### <a name="target-networks"></a>目标网络
根据这些设置，当你选择目标 VM 网络时，下表显示将可用的选项。

| **Select** | **受保护的云** | **提供保护的云** | **可用目标网络** |
| --- | --- | --- | --- |
| VMNetwork1-Chicago |SilverCloud1 |SilverCloud2 |可用 |
| GoldCloud1 |GoldCloud2 |可用 | |
| VMNetwork2-Chicago |SilverCloud1 |SilverCloud2 |不可用 |
| GoldCloud1 |GoldCloud2 |可用 | |

## <a name="multiple-subnets"></a>多个子网
如果目标网络具有多个子网，并且其中一个子网与源虚拟机所在的子网同名，则在故障转移后副本虚拟机将连接到该目标子网。 如果没有具有匹配名称的目标子网，则虚拟机将连接到网络中的第一个子网。

### <a name="failback"></a>故障回复
若要了解在故障回复（反向复制）情况下会发生，假设 VMNetwork1-NewYork 已映射到 VMNetwork1-Chicago，设置如下。

| **虚拟机** | **连接 VM 网络** |
| --- | --- |
| VM1 |VMNetwork1-Network |
| VM2（VM1 的副本） |VMNetwork1-Chicago |

在采用这些设置的条件下，我们看看在一些可能的方案中会发生什么情况。

| **方案** | **结果** |
| --- | --- |
| 在故障转移后，VM-2 的网络属性没有发生任何变化。 |VM-1 保持连接到源网络。 |
| 在故障转移后，VM-2 的网络属性发生变化，且连接断开。 |VM-1 断开连接。 |
| 在故障转移后，VM-2 的网络属性发生变化，且连接的是 VMNetwork2-Chicago。 |如果未映射 VMNetwork2-Chicago，VM-1 断开连接。 |
| VMNetwork1-Chicago 的网络映射发生变化。 |VM-1 连接现映射到 VMNetwork1-Chicago 的网络。 |

## <a name="next-steps"></a>后续步骤
现在，你已经对网络映射有更好的了解，可以[开始执行 Site Recovery 部署](site-recovery-best-practices.md)了。




<!--HONumber=Nov16_HO3-->


