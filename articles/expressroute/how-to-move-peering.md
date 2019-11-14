---
title: Azure ExpressRoute：将公共对等互连移到 Microsoft 对等互连
description: 本文介绍了在 ExpressRoute 上将公共对等互连移动到 Microsoft 对等互连的步骤。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f9ed841d347d99a2208971d164bbd7b9044e3b09
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031589"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>将公共对等互连移动到 Microsoft 对等互连

本文可帮助你在不停机的情况下将公共对等互连配置移动到 Microsoft 对等互连。 ExpressRoute 支持将 Microsoft 对等互连与 Azure PaaS 服务的路由筛选器结合使用，例如 Azure 存储和 Azure SQL 数据库。 你现在仅需要一个路由域即可访问 Microsoft PaaS 和 SaaS 服务。 可以利用路由筛选器为要使用的 Azure 区域选择性地播发 PaaS 服务前缀。

Azure 公共对等互连有 1 个 NAT IP 地址与每个 BGP 会话相关联。 通过 Microsoft 对等互连，可以配置自己的 NAT 分配，并使用路由筛选器进行选择性前缀播发。 公共对等互连是一种单向服务，使用该服务时，始终可以发起从 WAN 到 Microsoft Azure 服务的连接。 Microsoft Azure 服务无法通过此路由域发起到网络的连接。

启用公共对等互连后，可以连接到所有 Azure 服务。 我们不允许选择要将路由播发到的服务。 Microsoft 对等连接是一种双向连接，可以从 Microsoft Azure 服务以及 WAN 发起其连接。 有关路由域和对等互连的详细信息，请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)。

## <a name="before"></a>准备工作

若要连接到 Microsoft 对等互连，你需要设置和管理 NAT。 连接服务提供商可以将 NAT 作为托管服务进行设置和管理。 如果计划在 Microsoft 对等互连上访问 Azure PaaS 和 Azure SaaS 服务，请务必正确设置 NAT IP 池大小。 有关适用于 ExpressRoute 的 NAT 详细信息，请参阅 [Microsoft 对等互连的 NAT 要求](expressroute-nat.md#nat-requirements-for-microsoft-peering)。 通过 Azure ExpressRoute（Microsoft 对等互连）连接到 Microsoft 时，你有多条链路连接到 Microsoft。 一个链接是现有的 Internet 连接，另一个是通过 ExpressRoute 的连接。 发往 Microsoft 一些流量可能通过 Internet 传输，但通过 ExpressRoute 返回，反之亦然。

![双向连接](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> 已播发到 Microsoft 的 NAT IP 池不得播发到 Internet。 这会中断其他 Microsoft 服务的连接。

在配置 Microsoft 对等互连之前，请参阅[具有多个网络路径的非对称路由](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)了解非对称路由的注意事项。

* 如果使用公共对等互连，并且当前为用于访问 [Azure 存储](../storage/common/storage-network-security.md)或 [Azure SQL 数据库](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)的公共 IP 地址设置了 IP 网络规则，则需要确保配置了 Microsoft 对等互连的 NAT IP 池包含在 Azure 存储帐户或 Azure SQL 帐户的公共 IP 地址列表中。<br>
* 若要移动到 Microsoft 对等互连而不停机，则需按本文介绍的顺序使用这些步骤。

## <a name="create"></a>1. 创建 Microsoft 对等互连

如果尚未创建 Microsoft 对等互连，请使用以下任意文章创建 Microsoft 对等互连。 如果连接服务提供商提供第 3 层托管服务，则可以请求连接服务提供商为你的线路启用 Microsoft 对等互连。

如果第 3 层由你管理，则在继续操作之前需要以下信息：

* 主链路的 /30 子网。 这必须是你拥有的且已在 RIR/IRR 中注册的有效公共 IPv4 前缀。 在此子网中，Microsoft 将第二个可用的 IP 用于其路由器时，你将为你的路由器分配第一个可用的 IP 地址。<br>
* 辅助链路的 /30 子网。 这必须是你拥有的且已在 RIR/IRR 中注册的有效公共 IPv4 前缀。 在此子网中，Microsoft 将第二个可用的 IP 用于其路由器时，你将为你的路由器分配第一个可用的 IP 地址。<br>
* 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。 主要链接和次要链接必须使用相同的 VLAN ID。<br>
* 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。<br>
* 播发的前缀：必须提供要通过 BGP 会话播发的所有前缀列表。 只接受公共 IP 地址前缀。 如果打算发送一组前缀，可以发送逗号分隔列表。 这些前缀必须已在 RIR/IRR 中注册。<br>
* 路由注册表名称：可以指定 AS 编号和前缀要注册到的 RIR/IRR。

* **可选**-客户 ASN：如果要播发的前缀未注册到对等互连 AS 编号，可以指定将其注册到的 AS 编号。<br>
* **可选** - MD5 哈希（如果选择使用）。

有关启用 Microsoft 对等互连的详细说明，请参阅以下文章：

* [使用 Azure 门户创建 Microsoft 对等互连](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [使用 Azure Powershell 创建 Microsoft 对等互连](expressroute-howto-routing-arm.md#msft)<br>
* [使用 Azure CLI 创建 Microsoft 对等互连](howto-routing-cli.md#msft)

## <a name="validate"></a>2. 验证是否已启用 Microsoft 对等互连

验证 Microsoft 对等互连已启用，且播发的公用前缀处于已配置状态。

* [Azure 门户](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. 配置路由筛选器并将其附加到线路

默认情况下，新的 Microsoft 对等互连不播发任何前缀，直至路由筛选器附加到线路。 创建路由筛选器规则时，可以为要用于 Azure PaaS 服务的 Azure 区域指定服务社区列表。 这为你提供了根据需求筛选路由的灵活性，如以下屏幕截图所示：

![合并公共对等互连](./media/how-to-move-peering/routefilter.jpg)

使用以下任一文章配置路由筛选器：

* [使用 Azure 门户为 Microsoft 对等互连配置路由筛选器](how-to-routefilter-portal.md)<br>
* [使用 Azure PowerShell 为 Microsoft 对等互连配置路由筛选器](how-to-routefilter-powershell.md)<br>
* [使用 Azure CLI 为 Microsoft 对等互连配置路由筛选器](how-to-routefilter-cli.md)

## <a name="delete"></a>4. 删除公共对等互连

在验证 Microsoft 对等互连已配置且你要使用的前缀已在 Microsoft 对等互连上正确播发之后，随后即可删除公共对等互连。 若要删除公共对等互连，请使用以下任一文章：

* [使用 Azure 门户删除 Azure 公共对等互连](expressroute-howto-routing-portal-resource-manager.md#deletepublic)<br>
* [使用 Azure PowerShell 删除 Azure 公共对等互连](expressroute-howto-routing-arm.md#deletepublic)<br>
* [使用 CLI 删除 Azure 公共对等互连](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. 查看对等互连
  
在 Azure 门户中可以看到所有 ExpressRoute 线路和对等互连的列表。 有关详细信息，请参阅[查看 Microsoft 对等互连详细信息](expressroute-howto-routing-portal-resource-manager.md#getmsft)。

## <a name="next-steps"></a>后续步骤

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
