---
title: "设置使用 Azure Site Recovery 故障转移到辅助站点后要连接的 IP 地址 | Microsoft Docs"
description: "说明如何设置使用 Azure Site Recovery 故障转移到辅助站点后用于连接 VM 的 IP 地址。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: prateek9us
editor: 
ms.assetid: 67d73590-185c-49b2-a097-597bf54747a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: pratshar
ms.openlocfilehash: 6baeda08b1c41cc024a02f51ca27be2829c46962
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-a-secondary-site"></a>设置在故障转移到辅助站点后要连接的 IP 地址

查看部署先决条件后，阅读本文了解如何在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 将 System Center Virtual Machine Manager (VMM) 云中托管的 Hyper-V 虚拟机 (VM) 复制到辅助站点时计划网络。 

阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="overview"></a>概述

计划复制和故障转移策略时的关键问题之一是，如何在运行故障转移后连接到副本。 可通过以下几种方式实现： 

- 使用不同的 IP 地址：可选择对复制的 VM 使用不同的 IP 地址。 在此方案中，VM 会在故障转移后获取新的 IP 地址，并且需要进行 DNS 更新。
- 保留相同的 IP 地址：可能想要对副本 VM 使用相同的 IP 地址。 保留相同的 IP 地址，可以减少运行故障转移后出现的网络相关问题，从而简化恢复过程。 

## <a name="retaining-ip-addresses"></a>保留 IP 地址

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




