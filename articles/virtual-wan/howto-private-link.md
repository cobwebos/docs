---
title: 跨虚拟 WAN 共享专用链接服务
titleSuffix: Azure Virtual WAN
description: 在虚拟 WAN 中配置专用链接的步骤
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: fa4828d8b2752168d5f66a4f80c00611f80f0176
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306627"
---
# <a name="use-private-link-in-virtual-wan"></a>在虚拟 WAN 中使用专用链接

[Azure 专用链接](../private-link/private-link-overview.md)技术可用于通过公开[专用终结点](../private-link/private-endpoint-overview.md)，使用专用 IP 地址连接来连接 Azure 平台即服务产品。 使用 Azure 虚拟 WAN，可以在一个连接到任何虚拟中心的虚拟网络中部署专用终结点。 这提供了与连接到同一虚拟网络的任何其他虚拟 WAN 或分支的连接。

## <a name="before-you-begin"></a>开始之前

本文中的步骤假设你已部署了具有一个或多个中心的虚拟 WAN，并且至少有两个虚拟网络已连接到虚拟 WAN。

若要创建新虚拟 WAN 和新中心，请使用以下文章中的步骤：

* [创建虚拟 WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [创建中心](virtual-wan-site-to-site-portal.md#hub)
* [将 VNet 连接到中心](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>创建专用链接终结点

可以为许多不同服务创建专用链接终结点。 在此示例中，我们将使用 Azure SQL 数据库。 有关如何为 Azure SQL 数据库创建专用终结点的详细信息，请参阅[快速入门：使用 Azure 门户创建专用终结点](../private-link/create-private-endpoint-portal.md)。 下图显示了 Azure SQL 数据库的网络配置：

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="创建专用链接" lightbox="./media/howto-private-link/create-private-link.png":::

创建 Azure SQL 数据库之后，可以通过浏览专用终结点来验证专用终结点 IP 地址：

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="创建专用链接" lightbox="./media/howto-private-link/endpoints.png":::

单击已创建的专用终结点，应该会看到其专用 IP 地址以及完全限定的域名 (FQDN)。 请注意，专用终结点的 IP 地址在部署它的 VNet 范围 (10.1.3.0/24) 内：

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="创建专用链接" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>验证来自同一 VNet 的连接

在此示例中，我们将验证从安装了 MS SQL 工具的 Ubuntu 虚拟机到 Azure SQL 数据库的连接。 第一步是验证 DNS 解析是否有效，以及 Azure SQL 数据库完全限定的域名是否在部署了专用终结点的同一 VNet (10.1.3.0/24) 中解析为了专用 IP 地址：

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

如前面的输出中所示，FQDN `wantest.database.windows.net` 映射到了 `wantest.privatelink.database.windows.net`，随专用终结点一起创建的专用 DNS 区域将解析为专用 IP 地址 `10.1.3.228`。 可通过查看专用 DNS 区域来确认已将专用终结点的 A 记录映射到了专用 IP 地址：

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="创建专用链接" lightbox="./media/howto-private-link/dns-zone.png":::

验证正确的 DNS 解析后，可以尝试连接到数据库：

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

如你所见，我们使用的是一个特殊的 SQL 查询，该查询提供了 SQL 服务器从客户端看到的源 IP 地址。 在这种情况下，服务器会看到客户端及其专用 IP (`10.1.3.75`)，这意味着流量不会流经公共 Internet，而是直接进入专用终结点。

请注意，要使本指南中的示例生效，需要将变量 `username` 和 `password` 设置为与在 Azure SQL 数据库中定义的凭据匹配的值。

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>从其他 VNet 连接

既然 Azure 虚拟 WAN 中的一个 VNet 可以连接到专用终结点，那么连接到虚拟 WAN 的所有其他 VNet 和分支就也可以访问该终结点。 你需要通过 Azure 虚拟 WAN 支持的任何模型（举两个例子，[任意连接性方案](scenario-any-to-any.md)或[共享服务 VNet 方案](scenario-shared-services-vnet.md)）提供连接。

在 VNet 或分支与已部署专用终结点的 VNet 之间建立连接后，需要配置 DNS 解析：

* 如果从 VNet 连接到专用终结点，则可以使用通过 Azure SQL 数据库创建的同一专用区域。
* 如果从分支（站点到站点 VPN、点到站点 VPN 或 ExpressRoute）连接到专用终结点，则需要使用本地 DNS 解析。

在此示例中，我们将从另一个 VNet 连接，因此，首先我们将专用 DNS 区域附加到该新的 VNet，以便其工作负载能够将 Azure SQL 数据库完全限定的域名解析为专用 IP 地址。 此操作通过将专用 DNS 区域链接到新的 VNet 来完成：

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="创建专用链接" lightbox="./media/howto-private-link/dns-link.png":::

现在，附加 VNet 中的任何虚拟机都应将 Azure SQL 数据库 FQDN 正确解析为专用链接的专用 IP 地址：

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

为了再次检查此 VNet (10.1.1.0/24) 是否已连接到配置了专用终结点的原始 VNet (10.1.3.0/24)，可以验证 VNet 中任何虚拟机中的有效路由表：

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="创建专用链接" lightbox="./media/howto-private-link/effective-routes.png":::

如你所见，有一个路由指向由 Azure 虚拟 WAN 中的虚拟网络网关注入的 VNet 10.1.3.0/24。 现在，我们终于可以测试与数据库的连接了：

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

在此示例中，我们已了解如何在附加到虚拟 WAN 的一个 VNet 中创建专用终结点，从而提供与虚拟 WAN 中其余 VNet 和分支的连接。

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
