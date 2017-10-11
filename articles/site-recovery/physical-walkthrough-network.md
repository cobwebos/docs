---
title: "计划网络以便将物理服务器复制到 Azure | Microsoft Docs"
description: "本文介绍了将物理服务器复制到 Azure 时所必需的网络计划"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 71db2435-b5ce-4263-83f6-093d10d1d4e1
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: f8a20b45b50f71631122e574b634818c1912f12e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="step-4-plan-networking-for-physical-server-replication-to-azure"></a>第 4 步：计划网络以便将物理服务器复制到 Azure

本文总结了网络计划注意事项，以便使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地物理服务器复制到 Azure。

请将任何评论发布到本文底部，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提问。


## <a name="connect-to-replica-azure-vms"></a>连接到副本 Azure VM

计划复制和故障转移策略时的关键问题之一是，如何在运行故障转移后连接到 Azure VM。 设计有关副本 Azure VM 的网络策略时，可以选择使用下列两种方法：

- **使用不同的 IP 地址**：可以选择对复制的 Azure VM 网络使用不同的 IP 地址范围。 在此方案中，在运行故障转移后计算机会获得新 IP 地址，必须进行 DNS 更新。
- **使用相同的 IP 地址**：建议在运行故障转移后对 Azure 网络使用与本地主站点相同的 IP 地址范围。 保留相同的 IP 地址，可以减少运行故障转移后出现的网络相关问题，从而简化恢复过程。 不过，复制到 Azure 时，需要在运行故障转移后在路由中更新 IP 地址的新位置。

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


为了能够在保留 IP 地址的同时将其服务器复制到 Azure，Woodgrove 公司需要执行以下操作：

1. 创建 Azure 虚拟网络。 此网络应为本地网络扩展，这样应用程序才能顺畅地进行故障转移。
2. 除了允许向在 Azure 中创建的虚拟网络添加点到站点连接外，Azure 还允许添加站点间 VPN 连接。
3. 设置站点间连接时，只有当 IP 地址范围与本地 IP 地址范围不同时，才能在 Azure 网络中将流量路由到本地位置（本地网络）。
    - 这是因为 Azure 不支持延伸子网。 因此，如果在本地子网为 192.168.1.0/24，无法在 Azure 网络中添加本地网络 192.168.1.0/24。
    - 这是意料之中的，因为 Azure 不知道子网中没有活动的计算机，也不知道正在创建的子网仅用于灾难恢复。
    - 为了能够将流量从 Azure 网络中正确路由出去，Azure 网络和本地网络中的子网不得有冲突。

![运行子网故障转移前](./media/physical-walkthrough-network/network-design7.png)

#### <a name="before-failover"></a>在故障转移之前

1. 创建其他网络（例如，恢复网络）。 将在此网络中创建故障转移的 VM。
2. 为了确保计算机的 IP 地址会在运行故障转移后保留，请依次转到计算机属性和“配置”，指定服务器在本地使用的相同 IP 地址，再单击“保存”。
3. 对计算机进行故障转移时，Azure Site Recovery 将向它分配所提供的 IP 地址。
4. 触发故障转移并在 Azure 中创建分配有相应 IP 地址的 VM 后，可以使用 [Vnet 间连接](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)连接到网络。 可以为此操作编写脚本。
5. 需要适当修改路由，以反映 192.168.1.0/24 现已移到 Azure 中。

    ![运行子网故障转移后](./media/physical-walkthrough-network/network-design9.png)

#### <a name="after-failover"></a>在故障转移之后

如果没有上述 Azure 网络，可以在运行故障转移后创建主站点与 Azure 的站点间 VPN 连接。

## <a name="change-ip-addresses"></a>更改 IP 地址

这篇[博文](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)介绍了如何在运行故障转移后无需保留 IP 地址时设置 Azure 网络基础结构。 这篇博文从应用程序说明入手，介绍了如何设置本地网络和 Azure 网络，最后介绍了如何运行故障转移。  

## <a name="next-steps"></a>后续步骤

转到[第 5 步：准备 Azure 资源](physical-walkthrough-prepare-azure.md)
