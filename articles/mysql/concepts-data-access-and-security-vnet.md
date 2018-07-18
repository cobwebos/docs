---
title: Azure Database for MySQL 服务器 vnet 服务终结点概述 | Microsoft Docs
description: 介绍了 vnet 服务终结点如何为 Azure Database for MySQL 服务器工作。
services: mysql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.topic: conceptual
ms.date: 5/29/2018
ms.openlocfilehash: 652657c8891f2320c026251ffa32e4787028ee18
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658994"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mysql"></a>对 Azure Database for MySQL 使用虚拟网络服务终结点和规则

“虚拟网络规则”是一种防火墙安全功能，用于控制是否允许 Azure Database for MySQL 服务器接受从虚拟网络中的特定子网发送的通信。 本文说明了为何有时候最好选择虚拟网络规则功能来安全地允许与 Azure Database for MySQL 服务器的通信。

若要创建虚拟网络规则，首先必须存在可供规则引用的[虚拟网络][vm-virtual-network-overview] (VNet) 和[虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]。 下图说明了虚拟网络服务终结点如何与 Azure Database for MySQL 配合使用：

![VNet 服务终结点的工作原理示例](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> 对于 Azure Database for MySQL 来说，此功能在所有部署了 Azure Database for MySQL 的 Azure 公有云区域中都以公共预览版提供。

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>术语和说明

虚拟网络：可以让虚拟网络与 Azure 订阅相关联。

**子网:** 虚拟网络包含子网。 你所拥有的任何 Azure 虚拟机 (VM) 都会分配到子网。 一个子网可能包含多个 VM 或其他计算节点。 虚拟网络之外的计算节点不能访问虚拟网络，除非已将安全性配置为允许这样的访问。

虚拟网络服务终结点：[虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 本文介绍 **Microsoft.Sql** 的类型名称，即名为“SQL 数据库”的 Azure 服务。 此服务标记也适用于 Azure Database for MySQL 和 PostgreSQL 服务。 务必要注意的一点是，将 **Microsoft.Sql** 服务标记应用到 VNet 服务终结点时，它将为子网上的所有 Azure SQL 数据库、Azure Database for MySQL 和 Azure Database for PostgreSQL 服务器配置服务终结点流量。 

**虚拟网络规则：** 适用于 Azure Database for MySQL 服务器的虚拟网络规则是一个子网，该子网列在 Azure Database for MySQL 服务器的访问控制列表 (ACL) 中。 该子网必须包含 **Microsoft.Sql** 类型名称才会列在 Azure Database for MySQL 服务器的 ACL 中。

虚拟网络规则要求 Azure Database for MySQL 服务器接受来自该子网上所有节点的通信。







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>虚拟网络规则的优势

在你执行相应操作之前，子网上的 VM 不能与 Azure Database for MySQL 服务器通信。 建立通信的一项操作是创建虚拟网络规则。 若要弄清楚为何选择 VNet 规则方法，必须进行一个比较和对比式的讨论，其中涉及到防火墙提供的竞争性安全选项。

### <a name="a-allow-access-to-azure-services"></a>A. 允许访问 Azure 服务

“连接安全性”窗格有一个标签为“允许访问 Azure 服务”的“启用/禁用”按钮。 “启用”设置允许来自所有 Azure IP 地址和所有 Azure 子网的通信。 这些 Azure IP 或子网可能不是你所拥有的。 此“启用”设置可能超出你在开放方面对 Azure Database for MySQL 数据库的需要。 虚拟网络规则功能提供精细得多的控制。

### <a name="b-ip-rules"></a>B. IP 规则

可以通过 Azure Database for MySQL 防火墙指定 IP 地址范围，处于该范围内的通信允许进入 Azure Database for MySQL 数据库。 此方法适用于 Azure 专用网络外部的稳定 IP 地址。 但是，Azure 专用网络内部的许多节点都配置了动态 IP 地址。 某些情况下（例如重启 VM 时），动态 IP 地址可能会变化。 处于生产环境中时，在防火墙规则中指定一个动态 IP 地址并不明智。

可以通过获取 VM 的静态 IP 地址，对 IP 选项进行“补救”。 有关详细信息，请参阅[使用 Azure 门户为虚拟机配置专用 IP 地址][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]。

但是，静态 IP 方法可能会变得难以管理，在规模大时操作成本高。 虚拟网络规则更易于制定和管理。

### <a name="c-cannot-yet-have-azure-database-for-mysql-on-a-subnet-without-defining-a-service-endpoint"></a>C. 在没有定义服务终结点的情况下，子网上还不能有 Azure Database for MySQL

如果 **Microsoft.Sql** 服务器是虚拟网络子网上的一个节点，则该虚拟网络中的所有节点都可以与 Azure Database for MySQL 服务器通信。 在这种情况下，VM 可以与 Azure Database for MySQL 通信，而不需要任何虚拟网络规则或 IP 规则。

但截至 2018 年 5 月，Azure Database for MySQL 服务仍然无法直接分配给子网。

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>虚拟网络规则详细信息

此部分介绍虚拟网络规则的多项详细信息。

### <a name="only-one-geographic-region"></a>只有一个地理区域

一个虚拟网络服务终结点只能应用于一个 Azure 区域。 终结点不允许其他区域接受来自该子网的通信。

任何虚拟网络规则都只能应用于基础终结点应用到的区域。

### <a name="server-level-not-database-level"></a>服务器级而非数据库级

每个虚拟网络规则都适用于整个 Azure Database for MySQL 服务器，而不仅仅是该服务器上某个特定数据库。 换句话说，虚拟网络规则适用于服务器级而非数据库级。

### <a name="security-administration-roles"></a>安全管理角色

在管理虚拟网络服务终结点时，安全角色是分开的。 下述每个角色都需要进行操作：

- 网络管理员：&nbsp;启用终结点。
- 数据库管理员：&nbsp;更新访问控制列表 (ACL)，将给定的子网添加到 Azure Database for MySQL 服务器。

RBAC 备用：

网络管理员和数据库管理员角色的权限超出虚拟网络规则的管理需要， 只有部分权限是必需的。

可以选择在 Azure 中使用[基于角色的访问控制 (RBAC)][rbac-what-is-813s]，创建一个只有部分必需权限的自定义角色。 在涉及到网络管理员或数据库管理员时，可以使用自定义角色来代替。与向两个主要的管理员角色添加用户相比，向自定义角色添加用户的安全风险较低。

> [!NOTE]
> 在某些情况下，Azure Database for MySQL 和 VNet 子网位于不同的订阅中。 在这些情况下，必须确保以下配置：
> - 两个订阅都必须属于同一 Azure Active Directory 租户。
> - 用户具有启动操作所需的权限，例如启用服务终结点，以及向给定服务器添加 VNet-子网。

## <a name="limitations"></a>限制

对于 Azure Database for MySQL，虚拟网络规则功能具有以下限制：

- 在 Azure Database for MySQL 的防火墙中，每个虚拟网络规则都引用一个子网。 引用的所有这些子网都必须托管在同一个托管 Azure Database for MySQL 的地理区域内。

- 对于任何给定的虚拟网络，每个 Azure Database for MySQL 服务器最多可以有 128 个 ACL 条目。

- 虚拟网络规则仅适用于 Azure 资源管理器虚拟网络，不适用于[经典部署模型][arm-deployment-model-568f]网络。

- 使用 **Microsoft.Sql** 服务标记为 Azure Database for MySQL 启用虚拟网络服务终结点也会为所有 Azure 数据库服务（Azure Database for MySQL、Azure Database for PostgreSQL、Azure SQL 数据库和 Azure SQL 数据仓库）启用终结点。

- 在公共预览版期间，不支持 VNet 移动操作。 若要移动虚拟网络规则，请删除并重新创建它。

- 只有常规用途和内存优化服务器才支持 VNet 服务终结点。

- 在防火墙上，IP 地址范围适用于以下网络项，但虚拟网络规则并不适用：
    - [站点到站点 (S2S) 虚拟专用网络 (VPN)][vpn-gateway-indexmd-608y]
    - 通过 [Expressroute][expressroute-indexmd-744v] 进行的本地连接

## <a name="expressroute"></a>ExpressRoute

如果网络通过使用 [ ExpressRoute ][expressroute-indexmd-744v] 连接到 Azure 网络，则每个线路在 Microsoft Edge 配置有两个公共 IP 地址。 这两个 IP 地址用于通过使用 Azure 公共对等互连连接到 Azure 存储等 Microsoft 服务。

若要允许从线路到 Azure Database for MySQL 的通信，必须为线路的公共 IP 地址创建 IP 网络规则。 为查找 ExpressRoute 线路的公共 IP 地址，请使用 Azure 门户开具 ExpressRoute 支持票证。

## <a name="related-articles"></a>相关文章
- [Azure 虚拟网络][vm-virtual-network-overview]
- [Azure 虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>后续步骤
有关创建 VNet 规则的文章，请参阅：
- [使用 Azure 门户创建和管理 Azure Database for MySQL VNet 规则](howto-manage-vnet-using-portal.md)
- [使用 Azure CLI 创建和管理 Azure Database for MySQL VNet 规则](howto-manage-vnet-using-cli.md)

<!-- Link references, to text, Within this same Github repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml