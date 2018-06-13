---
title: 设置使用 Azure Site Recovery 故障转移到 Azure 后要连接的 IP 地址 | Microsoft Docs
description: 说明如何设置使用 Azure Site Recovery 从本地故障转移后用于连接到 Azure VM 的 IP 地址
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 9d4bf43ea727bbf7df79532499007dbd23eea925
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071631"
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-azure"></a>设置在故障转移到 Azure 后要连接的 IP 地址

本文介绍使用 [Azure Site Recovery](site-recovery-overview.md) 服务复制和故障转移到 Azure 后，用于连接 Azure VM 的网络要求。

本文介绍以下内容：

> [!div class="checklist"]
> * 可以使用的连接方法
> * 如何对已复制 Azure VM 使用不同 IP 地址
> * 如何在故障转移后保留 Azure VM 的 IP 地址

## <a name="connecting-to-replica-vms"></a>连接到副本 VM

在规划复制和故障转移策略时，其中一个关键问题是如何在故障转移后连接到 Azure VM。 在设计副本 Azure VM 的网络策略时，有以下几个选择：

- **使用不同的 IP 地址**：可以为复制的 Azure VM 网络选择使用不同的 IP 地址范围。 在此方案中，VM 会在故障转移后获取新的 IP 地址，并且需要进行 DNS 更新。
- **保留相同的 IP 地址**：你可能想要在故障转移后为 Azure 网络使用与本地主站点相同的 IP 地址范围。 保留相同的 IP 地址，可以减少运行故障转移后出现的网络相关问题，从而简化恢复过程。 不过，复制到 Azure 时，需要在运行故障转移后在路由中更新 IP 地址的新位置。

## <a name="retaining-ip-addresses"></a>保留 IP 地址

Site Recovery 支持在通过子网故障转移到 Azure 时保留固定的 IP 地址。

- 借助子网故障转移，特定子网会出现在站点 1 或站点 2 中，但永远不会同时出现在这两个站点中。
- 为了能够在故障转移时保留 IP 地址空间，可以编程方式安排路由器基础结构，将子网从一个站点移到另一个站点。
- 在故障转移期间，子网会随关联的受保护 VM 一起移动。 主要缺点是，在出现故障时，必须移动整个子网。


### <a name="failover-example"></a>故障转移示例

我们来看一个故障转移到 Azure 的示例，这里采用一个虚构的公司：Woodgrove Bank。

- Woodgrove Bank 在本地站点中托管商业应用程序。 它们在 Azure 上托管其移动应用。
- 其本地边缘网络和 Azure 虚拟网络之间存在 VPN 站点到站点连接。 由于存在 VPN 连接，Azure 中的虚拟网络显示为本地网络的扩展。
- Woodgrove 希望使用 Site Recovery 将本地工作负荷复制到 Azure。
 - Woodgrove 的应用依赖于硬编码 IP 地址，因此需要在故障转移到 Azure 后保留应用的 IP 地址。
 - Azure 中运行的资源使用 IP 地址范围 172.16.1.0/24、172.16.2.0/24。

![运行子网故障转移前](./media/site-recovery-network-design/network-design7.png)

**故障转移前的基础结构**


为了能使 Woodgrove 在保留 IP 地址的同时将其 VM 复制到 Azure，该公司需执行以下操作：


1. 创建 Azure 虚拟网络，故障转移本地计算机后将在其中创建 Azure VM。 此网络应为本地网络扩展，这样应用程序才能顺畅地进行故障转移。
2. 在 Site Recovery 中故障转移前，将在计算机属性中分配相同 IP 地址。 故障转移后，Site Recovery 将此地址分配给 Azure VM。
3. 运行故障转移并使用相同 IP 地址创建 Azure VM 后，这些 VM 将使用 [Vnet 到 Vnet 连接](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)连接到网络。 此操作可以编写脚本。
4. 它们需要修改路由，以反映 192.168.1.0/24 现在已移到 Azure。


**故障转移后的基础结构**

![运行子网故障转移后](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>站点到站点连接

除了 vnet 到 vnet 连接外，故障转移后，Woodgrove 还可以设置站点到站点 VPN 连接：
- 设置站点到站点连接时，只有当 IP 地址范围与本地 IP 地址范围不同时，才能在 Azure 网络中将流量路由到本地位置（本地网络）。 这是因为 Azure 不支持延伸子网。 因此，如果在本地有一个子网 192.168.1.0/24，则不能在 Azure 网络中添加一个本地网络 192.168.1.0/24。 这是意料之中的，因为 Azure 不知道子网中没有活动的 VM，也不知道正在创建的子网仅用于灾难恢复。
- 为了能够将网络流量从一个 Azure 网络中正确路由出去，该网络中的子网和本地网络中的子网不能有冲突。




## <a name="assigning-new-ip-addresses"></a>分配新 IP 地址

这篇[博文](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)介绍了如何在运行故障转移后无需保留 IP 地址时设置 Azure 网络基础结构。 这篇博文从应用程序说明入手，介绍了如何设置本地网络和 Azure 网络，最后介绍了如何运行故障转移。

## <a name="next-steps"></a>后续步骤
[运行故障转移](site-recovery-failover.md)
