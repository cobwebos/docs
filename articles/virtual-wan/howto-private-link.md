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
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91306627"
---
# <a name="use-private-link-in-virtual-wan"></a>使用虚拟 WAN 中的专用链接

[Azure Private Link](../private-link/private-link-overview.md) 是一种技术，允许你通过公开 [专用终结点](../private-link/private-endpoint-overview.md)来使用专用 IP 地址连接来连接 Azure 平台即服务产品。 使用 Azure 虚拟 WAN，可以在连接到任何虚拟中心的虚拟网络之一中部署专用终结点。 这可以连接到连接到同一虚拟网络的任何其他虚拟网络或分支。

## <a name="before-you-begin"></a>开始之前

本文中的步骤假设你已部署具有一个或多个集线器的虚拟 WAN，以及至少两个连接到虚拟 WAN 的虚拟网络。

若要创建新虚拟 WAN 和新中心，请使用以下文章中的步骤：

* [创建虚拟 WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [创建中心](virtual-wan-site-to-site-portal.md#hub)
* [将 VNet 连接到中心](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>创建专用链接终结点

你可以为许多不同的服务创建专用链接终结点。 在此示例中，我们将使用 Azure SQL 数据库。 可以在 [快速入门：使用 Azure 门户创建专用终结点](../private-link/create-private-endpoint-portal.md)中找到有关如何为 Azure SQL 数据库创建专用终结点的详细信息。 下图显示了 Azure SQL 数据库的网络配置：

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="创建专用链接" lightbox="./media/howto-private-link/create-private-link.png":::

创建 Azure SQL 数据库后，可以验证专用终结点 IP 地址浏览专用终结点：

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="专用终结点" lightbox="./media/howto-private-link/endpoints.png":::

单击已创建的专用终结点，你应该会看到其专用 IP 地址以及其完全限定的域名 (FQDN) 。 请注意，专用终结点在 VNet 的范围内有一个 IP 地址，该地址已部署 (10.1.3.0/24) ：

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="SQL 端点" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>验证来自同一 VNet 的连接

在此示例中，我们将验证是否从安装了 MS SQL 工具的 Ubuntu 虚拟机连接到 Azure SQL 数据库。 第一步是验证 DNS 解析是否有效，并将 Azure SQL 数据库完全限定的域名解析为专用 IP 地址，该地址在已部署专用终结点的同一 VNet 中 (10.1.3.0/24) ：

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

如前面的输出中所示，FQDN `wantest.database.windows.net` 映射到 `wantest.privatelink.database.windows.net` ，通过专用终结点创建的专用 DNS 区域将解析为专用 IP 地址 `10.1.3.228` 。 查看专用 DNS 区域将确认已将专用终结点的 A 记录映射到专用 IP 地址：

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="DNS 区域" lightbox="./media/howto-private-link/dns-zone.png":::

验证正确的 DNS 解析后，可以尝试连接到数据库：

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

正如您所看到的，我们使用的是一个特殊的 SQL 查询，它为我们提供了 SQL server 从客户端看到的源 IP 地址。 在这种情况下，服务器会看到客户端及其专用 IP (`10.1.3.75`) ，这意味着流量不会流经公共 Internet，而是直接进入专用终结点。

请注意，若要 `username` `password` 使本指南中的示例起作用，需要设置变量并匹配 Azure SQL 数据库中定义的凭据。

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>从其他 VNet 连接

由于 Azure 虚拟 WAN 中的一个 VNet 已经连接到专用终结点，因此，连接到虚拟 WAN 的其他所有 Vnet 和分支都可以访问它。 需要通过 Azure 虚拟 WAN 支持的任何模型（如 " [任意到任意方案](scenario-any-to-any.md) " 或 " [共享服务 VNet" 方案](scenario-shared-services-vnet.md)）提供连接，以命名两个示例。

在 VNet 或分支之间连接到已部署专用终结点的 VNet 后，需要配置 DNS 解析：

* 如果从 VNet 连接到专用终结点，则可以使用通过 Azure SQL 数据库创建的同一专用区域。
* 如果从分支连接到专用终结点 (站点到站点 VPN、点到站点 VPN 或 ExpressRoute) ，则需要使用本地 DNS 解析。

在此示例中，我们将从另一个 VNet 连接，因此，首先我们将专用 DNS 区域附加到新的 VNet，以便其工作负荷能够将 Azure SQL 数据库完全限定的域名解析为专用 IP 地址。 这是通过将专用 DNS 区域链接到新 VNet 来完成的：

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="DNS 链接" lightbox="./media/howto-private-link/dns-link.png":::

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

若要仔细检查此 VNet (10.1.1.0/24) 是否已连接到在其中配置了专用终结点的原始 VNet (10.1.3.0/24) ，可以验证 VNet 中任何虚拟机中的有效路由表：

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="有效路由" lightbox="./media/howto-private-link/effective-routes.png":::

正如您所看到的，有一个路由指向由 Azure 虚拟网络中的虚拟网络网关注入的 VNet 10.1.3.0/24。 现在，我们可以测试与数据库的连接：

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

在此示例中，我们已了解如何在附加到虚拟 WAN 的某个 Vnet 中创建专用终结点，从而提供与虚拟 WAN 中的其余 Vnet 和分支的连接。

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
