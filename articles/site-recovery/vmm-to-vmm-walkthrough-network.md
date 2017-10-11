---
title: "规划网络以使用 Azure Site Recovery 将 Hyper-V 复制到辅助 VMM 站点 | Microsoft Docs"
description: "本文介绍使用 Azure Site Recovery 将 Hyper-V VM 复制到辅助 System Center VMM 站点时的网络规划。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 095f2d73-994e-4fc0-96f2-e03a7d72e492
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: a1f3f6e6cba074647195e2b0cbcdc7b4f3dec475
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="step-3-plan-networking-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>步骤 3：规划网络以将 Hyper-V VM 复制到辅助 VMM 站点

查看部署先决条件后，阅读本文了解如何在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 将 System Center Virtual Machine Manager (VMM) 云中托管的 Hyper-V 虚拟机 (VM) 复制到辅助站点时计划网络。 

阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="network-mapping-overview"></a>网络映射概述

将 Hyper-V VM（托管在 VMM 中）复制到辅助站点时会用到网络映射。 网络映射在源 VMM 服务器上的 VM 网络和目标 VMM 服务器上的 VM 网络之间进行映射。 映射可执行以下操作：

- **网络连接**：在故障转移后将 VM 连接到适当的网络。 副本 VM 会连接到已映射到源网络的目标网络。
- **最佳位置**：以最佳方式在 Hyper-V 主机服务器上放置副本 VM。 副本 VM 放在可访问映射的 VM 网络的主机上。
- **无网络映射**：如果未配置网络映射，则故障转移后，副本 VM 不会连接任何 VM 网络。


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



## <a name="prepare-for-network-mapping"></a>准备网络映射

1. 在源和目标 VMM 服务器上，应具有与源和目标云关联的逻辑网络。 
2. 在源和目标服务器中，应具有链接到逻辑网络的 VM 网络。
3. 应该将源位置中 Hyper-V 主机上的 VM 链接到源 VM 网络。 如果仅使用一个 VMM 服务器，则可配置同一服务器上 VM 网络之间的映射。

如果在 Site Recovery 部署期间设置网络映射，将发生以下情况：

- 设置网络映射并选择目标 VM 网络时，将显示使用源 VM 网络的 VMM 源云，以及目标云上的可用目标 VM 网络。
- - 在正确配置映射并启用复制后，源 VM 将连接到其源 VM 网络，而它位于目标位置的副本将连接到其映射 VM 网络。
- 如果目标网络具有多个子网，并且其中一个子网与源虚拟机所在的子网同名，则在故障转移后副本虚拟机将连接到该目标子网。 如果没有具有匹配名称的目标子网，则 VM 将连接到网络中的第一个子网。

## <a name="connect-to-vms-after-failover"></a>故障转移后连接到 VM

计划复制和故障转移策略时的关键问题之一是，如何在运行故障转移后连接到副本。 可通过以下几种方式实现： 

- 使用不同的 IP 地址：可选择对复制的 VM 使用不同的 IP 地址。 在此方案中，VM 会在故障转移后获取新的 IP 地址，并且需要进行 DNS 更新。
- 保留相同的 IP 地址：可能想要对副本 VM 使用相同的 IP 地址。 保留相同的 IP 地址，可以减少运行故障转移后出现的网络相关问题，从而简化恢复过程。 

## <a name="retain-ip-addresses"></a>保留 IP 地址

如果想要在故障转移到辅助站点后保留主站点的 IP 地址，可执行完整的子网故障转移并更新路由，指示 IP 地址的新位置，也可选择在主站点和恢复站点之间部署外延式子网。

### <a name="stretched-subnet"></a>外延式子网

在外延式子网中，子网可在主站点和辅助站点中同时使用。 如果将一个服务器及其 IP（第 3 层）配置移动到辅助站点，网络会自动将流量路由到新的位置。 

从第 2 层（数据链路层）的角度来看，需要可以管理外延式 VLAN 的网络设备。 此外，通过延伸 VLAN，潜在容错域扩展到两个站点，本质上变为单一故障点。 虽然这不太可能，但出现广播风暴且无法隔离的情况却可能发生。 


### <a name="subnet-failover"></a>子网故障转移

可以运行子网故障转移，获得外延式子网的优势，但实际上不对其进行延伸。 在此解决方案中，可在源或目标站点中使用子网，但不可同时使用。 为了能够在故障转移时保留 IP 地址空间，可以编程方式安排路由器基础结构，将子网从一个站点移到另一个站点。 发生故障转移后，子网将与关联的 VM 一起移动。 主要缺点是，在出现故障时，必须移动整个子网。

### <a name="example"></a>示例

下面是一个完整的子网故障转移示例。 主站点具有在子网 192.168.1.0/24 中运行的应用程序。 在故障转移时，此子网中的所有 VM 都将故障转移到辅助站点，并保留其 IP 地址。 必须修改路由，以反映所有属于子网 192.168.1.0/24 的虚拟机 (VM) 现都已移至辅助站点的事实。

下图显示故障转移前后的子网：

- 故障转移前，子网 192.168.0.1/24 在源站点处于活动状态，而故障转移后将在辅助站点变为活动状态。
- 需要适当修改主站点和恢复站点、第三站点和主站点以及第三站点和恢复站点之间的路由。

故障转移前

![在故障转移之前](./media/vmm-to-vmm-walkthrough-network/network-design2.png)

故障转移后

![在故障转移之后](./media/vmm-to-vmm-walkthrough-network/network-design3.png)

故障转移后，将发生以下情况：

- Site Recovery 从每个 VMM 实例的相关网络中的静态 IP 地址池为 VM 上的每个网络接口分配一个 IP 地址。
- 如果辅助站点的 IP 地址池与源站点的 IP 地址池相同，Site Recovery 将向副本 VM 分配与源 VM相同的 IP 地址。 IP 地址保留在 VMM 中，但未设置为 Hyper-V 主机上的故障转移 IP 地址。 Hyper-V 主机上的故障转移 IP 地址会在故障转移之前设置。
- 如果相同 IP 地址不可用，Site Recovery 将分配该池中的另一可用 IP 地址。
- 如果 VM 使用 DHCP，则 Site Recovery 不管理 IP 地址。 需要检查确定辅助站点上的 DHCP 服务器可分配与源站点相同范围的地址。

### <a name="validate-the-ip-address"></a>验证 IP 地址

为 VM 启用保护后，可以使用以下示例脚本来验证分配给 VM 的地址。 相同的 IP 地址将被设为故障转移 IP 地址，并在故障转移期间分配给 VM：

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="changing-ip-addresses"></a>更改 IP 地址

在此方案中，将更改故障转移的 VM 的 IP 地址。 此解决方案的缺点是需要维护。 通常情况下，副本 VM 启动后，将更新 DNS。 DNS 条目可能需要更改，否则会在网络中变得混乱，并且更新缓存条目。 这可能导致停机。 可按照以下方式缓解停机：

- 对 Intranet 应用程序使用低 TTL 值。
- 在 Site Recovery 恢复计划中使用以下脚本更新 DNS 服务器，确保及时更新。 如果使用动态 DNS 注册，则不需要该脚本。

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>示例 

我们来看一个方案，该方案计划在主站点和恢复站点中使用不同的 IP 地址。在此示例中，主站点和辅助站点的 IP 地址不同，并且存在一个第三站点，可通过该站点访问托管于主站点或辅助站点上的应用程序。

- 故障转移前，应用托管于主站点上的子网 192.168.1.0/24 中，而故障转移后，应用将配置为托管于辅助站点上的子网 172.16.1.0/24 中。
- 已经正确配置 VPN 连接/网络路由，使所有三个站点都能相互访问。
- 故障转移后，应用将在恢复子网中还原。 在此方案中，无需故障转移整个子网，并且无需进行重新配置 VPN 或网络路由的更改。 故障转移和部分 DNS 更新会确保应用程序仍然可供访问。
- 如果 DNS 配置为允许动态更新，则 VM 会在故障转移后启动时使用新的 IP 地址自行注册。

故障转移前

![不同的 IP — 在故障转移之前](./media/vmm-to-vmm-walkthrough-network/network-design10.png)

故障转移后

![不同的 IP — 在故障转移之后](./media/vmm-to-vmm-walkthrough-network/network-design11.png)



## <a name="next-steps"></a>后续步骤

请转到[步骤 4：准备 VMM 和 Hyper-V](vmm-to-vmm-walkthrough-vmm-hyper-v.md)。


