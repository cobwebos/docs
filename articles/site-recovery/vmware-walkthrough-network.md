---
title: "为 VMware 到 Azure 的复制规划网络 | Microsoft Docs"
description: "本文讨论在将 VMware VM 复制到 Azure 时所需要进行的网络规划"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5578a0b4-0352-41c3-9cce-56beb17a4d97
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: f164ac68ba6ec650bb3996b4aa870e1b98533a23
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017


---

# <a name="step-4-plan-networking-for-vmware-to-azure-replication"></a>步骤 4：为 VMware 到 Azure 的复制规划网络

本文总结了在使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 VMware VM 复制到 Azure 时的网络规划注意事项。

请将任何评论发布到本文底部，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提问。


## <a name="connect-to-replica-vms"></a>连接到副本 VM

在规划复制和故障转移策略时，其中一个关键问题是如何在故障转移后连接到 Azure VM。 在设计副本 Azure VM 的网络策略时，有以下几个选择：

- **使用不同的 IP 地址**：可以为复制的 Azure VM 网络选择使用不同的 IP 地址范围。 在此方案中，VM 会在故障转移后获取新的 IP 地址，并且需要进行 DNS 更新。
- **保留相同的 IP 地址**：你可能想要在故障转移后为 Azure 网络使用与本地主站点相同的 IP 地址范围。 保留相同的 IP 地址，通过减少故障转移后网络相关问题来简化恢复过程。 但是，在复制到 Azure 时，需要在故障转移后使用 IP 地址的新位置来更新路由。 


## <a name="retain-ip-addresses"></a>保留 IP 地址

在通过子网故障转移方式故障转移到 Azure 时，Site Recovery 提供保留固定 IP 地址的功能。

借助子网故障转移，特定子网会出现在站点 1 或站点 2 中，但永远不会同时出现在这两个站点中。 为了在故障转移时保留 IP 地址空间，可以通过编程方式安排路由器基础结构将子网从一个站点移到另一个站点。 在故障转移期间，子网随关联的受保护 VM 一起移动。 主要缺点是，在出现故障时，必须移动整个子网。


### <a name="failover-example"></a>故障转移示例

我们来看一个故障转移到 Azure 的示例。

- Woodgrove Bank（一个虚构的公司）具有用于托管其商业应用程序的本地基础结构。 其移动应用程序托管在 Azure 上。
- Azure 中的 Woodgrove Bank VM 以及本地服务器之间的连接由本地边缘网络和 Azure 虚拟网络之间的站点到站点 (VPN) 连接提供。
- 此 VPN 意味着 Azure 中的公司虚拟网络看上去就是其本地网络的扩展。
- Woodgrove 想要使用 Site Recovery 将本地工作负载复制到 Azure。
 - Woodgrove 必须处理依赖于硬编码 IP 地址的应用程序和配置，因此需要在向 Azure 进行故障转移之后为他们的应用程序保留 IP 地址。
 - Woodgrove 已将来自 IP 地址范围（172.16.1.0/24、172.16.2.0/24）的 IP 地址分配给在 Azure 中运行的资源。


为了能使 Woodgrove 在保留 IP 地址的同时将其 VM 复制到 Azure，该公司需要执行以下操作：

1. 创建 Azure 虚拟网络。 此网络应该是本地网络的扩展，这样应用程序才可以无缝地进行故障转移。
2. 除添加点到站点连接能力外，Azure 还允许将站点到站点 VPN 连接能力添加到在 Azure 中创建的虚拟网络。
3. 设置站点到站点连接时，只有在 IP 地址范围与本地 IP 地址范围不同时，才可以将 Azure 网络的流量路由到本地位置（本地网络）。
    - 这是因为 Azure 不支持延伸的子网。 因此，如果在本地有一个子网 192.168.1.0/24，则不能在 Azure 网络中添加一个本地网络 192.168.1.0/24。
    - 这是意料之中的，因为 Azure 不知道子网中没有活动的 VM，也不知道正在创建的子网仅用于灾难恢复。
    - 为了能够将网络流量从一个 Azure 网络中正确路由出去，该网络中的子网和本地网络中的子网不能有冲突。

![在子网故障转移之前](./media/site-recovery-network-design/network-design7.png)

### <a name="before-failover"></a>在故障转移之前

1. 再创建一个网络（例如恢复网络）。 后面要在该网络中创建故障转移 VM。
2. 为了确保 VM 的 IP 地址会在故障转移之后保留，请在 VM 属性下的“配置”中指定 VM 在本地使用的相同 IP 地址，然后单击“保存”。
3. 当 VM 故障转移时，Azure Site Recovery 将为其分配所提供的 IP 地址。

    ![网络属性](./media/site-recovery-network-design/network-design8.png)

4. 触发故障转移，并在 Azure 中创建带有所需 IP 地址的 VM 后，可以连接到使用 [Vnet 到 Vnet 连接](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)的网络。 此操作可以编写脚本。
5. 路由需要进行适当的修改，以反映 192.168.1.0/24 现在已移动到 Azure。

    ![在子网故障转移之后](./media/site-recovery-network-design/network-design9.png)

### <a name="after-failover"></a>在故障转移之后

如果没有上述 Azure 网络，可以在故障转移后创建主站点和 Azure 之间的站点到站点 VPN 连接。

## <a name="change-ip-addresses"></a>更改 IP 地址

这篇[博客文章](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)介绍如何在故障转移后无需保留 IP 地址的情况下设置 Azure 网络基础结构。 该文章从介绍应用程序说明开始，讨论如何设置本地和 Azure 中的网络，并总结了有关运行故障转移的信息。  

## <a name="next-steps"></a>后续步骤

转到[步骤 5：准备 Azure](vmware-walkthrough-prepare-azure.md)

