---
title: "规划网络以使用 Azure Site Recovery 将 Hyper-V（包含 System Center VMM）复制到 Azure | Microsoft Docs"
description: "本文介绍在将 Hyper-V VM（包含 VMM）复制到 Azure 时所需要进行的网络规划"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 41c3c83e-6b1a-496a-8179-498c552ef0c7
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ef87cd82b021e40f0da05142878daff245cd9c62
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---

# <a name="step-4-plan-networking-for-hyper-v-with-vmm-to-azure-replication"></a>步骤 4：规划网络以将 Hyper-V（包含 VMM）复制到 Azure

执行[容量规划](vmm-to-azure-walkthrough-capacity.md)（若要执行完整部署）后，阅读本文了解使用 [Azure Site Recovery](site-recovery-overview.md)服务将 System Center Virtual Machine Manager (VMM) 云中的本地 Hyper-V VM 时复制到 Azure 时需考虑的网络规划注意事项。

请将任何评论发布到本文底部，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提问。


## <a name="network-mapping-for-replication-to-azure"></a>复制到 Azure 的网络映射

将 Hyper-V VM（在 VMM 中管理）复制到 Azure 时会用到网络映射。 网络映射在源 VMM 服务器上的 VM 网络和目标 Azure 网络之间进行映射。 映射可执行以下操作：

- 网络连接：故障转移后，所有复制的 Azure VM 均已连接到映射的 Azure 网络。 在同一网络上进行故障转移的所有计算机都可互相连接，即使这些计算机在不同的恢复计划中进行故障转移。
- 网络网关：如果在目标 Azure 网络上设置了网络网关，VM 可连接到映射本地网络中的其他本地虚拟机。

### <a name="prepare-vmm-for-network-mapping"></a>为网络映射准备 VMM

为网络映射准备 VMM，如下所示：

1. 如果没有 VMM，请准备与 Hyper-V 主机所在云关联的 [VMM 逻辑网络](https://docs.microsoft.com/system-center/vmm/network-logical)。
2. 如果没有，请创建链接到前面准备的逻辑网络的 [VM 网络](https://docs.microsoft.com/system-center/vmm/network-virtual)。
3. 将 VMM 云中 Hyper-V 主机服务器/群集上的 VM 连接到 VM 网络。

 
请注意： 
- 执行复制时，添加到源 VM 网络的新 VM 会连接到映射的 Azure 网络。
- 如果目标网络具有多个子网，并且其中一个子网与源虚拟机所在的子网同名，则在故障转移后副本虚拟机将连接到该目标子网。
- 如果没有具有匹配名称的目标子网，则虚拟机将连接到网络中的第一个子网。
- 部署方案时，准备 Azure 网络，并设置网络映射。

## <a name="connecting-to-azure-vms-after-failover"></a>故障转移后连接到 Azure VM

计划复制和故障转移策略时的关键问题之一是，如何在运行故障转移后连接到 Azure VM。 设计有关副本 Azure VM 的网络策略时，可以选择使用下列两种方法：

- **使用不同的 IP 地址**：可以选择对复制的 Azure VM 网络使用不同的 IP 地址范围。 在此方案中，VM 会在故障转移后获取新的 IP 地址，并且需要进行 DNS 更新。
- 保留相同 IP 地址：可能想要在故障转移后为 Azure 网络使用与本地主网络相同的 IP 地址范围。  保留相同的 IP 地址，可以减少运行故障转移后出现的网络相关问题，从而简化恢复过程。 不过，复制到 Azure 时，需要在运行故障转移后在路由中更新 IP 地址的新位置。


## <a name="retain-ip-addresses"></a>保留 IP 地址

Site Recovery 支持在通过子网故障转移到 Azure 时保留固定的 IP 地址。

借助子网故障转移，特定子网会出现在站点 1 或站点 2 中，但永远不会同时出现在这两个站点中。 为了能够在故障转移时保留 IP 地址空间，可以编程方式安排路由器基础结构，将子网从一个站点移到另一个站点。 在故障转移期间，子网会随关联的受保护 VM 一起移动。 主要缺点是，在出现故障时，必须移动整个子网。



### <a name="failover-example"></a>故障转移示例

下面的示例展示了如何故障转移到 Azure。

- 假设公司 Woodgrove Bank 使用本地基础结构托管商业应用程序。 移动应用程序托管在 Azure 上。
- Azure 中的 Woodgrove Bank VM 与本地服务器的连接由本地边缘网络与 Azure 虚拟网络的站点间 (VPN) 连接提供。
- 此 VPN 意味着，公司在 Azure 中的虚拟网络以本地网络扩展的形式显示。
- Woodgrove 希望使用 Site Recovery 将本地工作负载复制到 Azure。
 - Woodgrove 必须处理依赖硬编码 IP 地址的应用程序和配置，因此需要在故障转移到 Azure 之后为应用程序保留 IP 地址。
 - Woodgrove 已向在 Azure 中运行的资源分配介于范围 172.16.1.0/24、172.16.2.0/24 的 IP 地址。


为了能使 Woodgrove 在保留 IP 地址的同时将其 VM 复制到 Azure，该公司需执行以下操作：

1. 创建 Azure 虚拟网络。 此网络应为本地网络扩展，这样应用程序才能顺畅地进行故障转移。
2. 除了允许向在 Azure 中创建的虚拟网络添加点到站点连接外，Azure 还允许添加站点间 VPN 连接。
3. 设置站点间连接时，只有当 IP 地址范围与本地 IP 地址范围不同时，才能在 Azure 网络中将流量路由到本地位置（本地网络）。
    - 这是因为 Azure 不支持延伸子网。 因此，如果在本地有一个子网 192.168.1.0/24，则不能在 Azure 网络中添加一个本地网络 192.168.1.0/24。
    - 这是意料之中的，因为 Azure 不知道子网中没有活动的 VM，也不知道正在创建的子网仅用于灾难恢复。
    - 为了能够将网络流量从一个 Azure 网络中正确路由出去，该网络中的子网和本地网络中的子网不能有冲突。

![运行子网故障转移前](./media/vmm-to-azure-walkthrough-network/network-design7.png)

### <a name="before-failover"></a>在故障转移之前

1. 创建其他网络（例如，恢复网络）。 后面要在该网络中创建故障转移 VM。
2. 为了确保 VM 的 IP 地址会在故障转移之后保留，请在 VM 属性下的“配置”中指定 VM 在本地使用的相同 IP 地址，然后单击“保存”。
3. 当 VM 故障转移时，Azure Site Recovery 将为其分配所提供的 IP 地址。

    ![网络属性](./media/vmm-to-azure-walkthrough-network/network-design8.png)

4. 触发故障转移，并在 Azure 中使用所需的 IP 地址创建 VM 后，可以使用 [Vnet 到 Vnet 连接](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)连接到该网络。 此操作可以编写脚本。
5. 需要适当修改路由，以反映 192.168.1.0/24 现已移到 Azure 中。

    ![运行子网故障转移后](./media/vmm-to-azure-walkthrough-network/network-design9.png)

### <a name="after-failover"></a>在故障转移之后

如果没有上述 Azure 网络，可以在故障转移后创建主站点和 Azure 之间的站点到站点 VPN 连接。

## <a name="change-ip-addresses"></a>更改 IP 地址

这篇[博文](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)介绍了如何在运行故障转移后无需保留 IP 地址时设置 Azure 网络基础结构。 这篇博文从应用程序说明入手，介绍了如何设置本地网络和 Azure 网络，最后介绍了如何运行故障转移。  

## <a name="next-steps"></a>后续步骤

转到[第 5 步：准备 Azure 资源](vmm-to-azure-walkthrough-prepare-azure.md)

