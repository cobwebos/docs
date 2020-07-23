---
title: 将应用程序连接到 SQL 托管实例
titleSuffix: Azure SQL Managed Instance
description: 本文介绍如何将应用程序连接到 Azure SQL 托管实例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: a5d002532adb043fa5196231964d5b6e2c81417c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84706369"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>将应用程序连接到 Azure SQL 托管实例
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

在确定如何以及在何处托管应用程序时，目前有多个选项可供选择。

你可以选择在云中托管应用程序，方法是使用 Azure App Service 或某些 Azure 虚拟网络集成选项，如 Azure 应用服务环境、Azure 虚拟机和虚拟机规模集。 还可以采用混合云方法，将应用程序保留在本地。

无论进行哪种选择，都可以将其连接到 Azure SQL 托管实例。 

![高可用性](./media/connect-application-instance/application-deployment-topologies.png)

本文介绍如何在许多不同的应用程序方案中将应用程序连接到 Azure SQL 托管实例。 

## <a name="connect-inside-the-same-vnet"></a>在同一 VNet 内连接

将应用程序连接到与 SQL 托管实例相同的虚拟网络中是最简单的方案。 虚拟网络内的虚拟机可以直接连接到其他虚拟机，即使它们位于不同的子网中。 这意味着，你只需在应用服务环境或虚拟机内连接应用程序，就可以相应地设置连接字符串。  

## <a name="connect-inside-a-different-vnet"></a>在不同的 VNet 中连接

当应用程序位于与 SQL 托管实例不同的虚拟网络中时，将其连接起来稍微复杂一些，因为 SQL 托管实例在其自己的虚拟网络中具有专用 IP 地址。 若要进行连接，应用程序需要对部署 SQL 托管实例的虚拟网络的访问权限。 因此，你需要在应用程序与 SQL 托管实例虚拟网络之间建立连接。 虚拟网络无需位于同一订阅中，即可使用此方案。

连接虚拟网络有两个选项：

- [Azure VPN 对等互连](../../virtual-network/virtual-network-peering-overview.md)
- VNet 到 VNet VPN 网关（[Azure 门户](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)、[PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)、[Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)）

对等互连是首选方法，因为它使用 Microsoft 主干网络，因此从连接角度来看，对等互连虚拟网络中的虚拟机与同一虚拟网络中的虚拟机之间不存在明显的延迟。 虚拟网络对等互连仅限于同一区域中的网络。  

> [!IMPORTANT]
> SQL 托管实例的虚拟网络对等互连方案仅限同一区域的网络，因为[全局虚拟网络对等互连的限制](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)。 有关更多详细信息，另请参阅[Azure 虚拟网络常见问题解答](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)一文中的相关部分。 

## <a name="connect-from-on-premises"></a>从本地连接 

你还可以将本地应用程序连接到 SQL 托管实例。 仅可通过专用 IP 地址访问 SQL 托管实例。 若要从本地访问，需要在应用程序与 SQL 托管实例虚拟网络之间建立站点到站点连接。

有两个选项可用于将本地连接到 Azure 虚拟网络：

- 站点到站点 VPN 连接（[Azure 门户](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)、 [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)、 [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)）
- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)连接  

如果已成功建立本地到 Azure 的连接，并且无法建立与 SQL 托管实例的连接，请检查你的防火墙在 SQL 端口1433上是否有打开的出站连接，以及用于重定向的11000-11999 端口范围。

## <a name="connect-the-developer-box"></a>连接开发人员箱

还可以将开发人员机箱连接到 SQL 托管实例。 只能通过专用 IP 地址访问 SQL 托管实例，因此，若要从 "开发人员" 框中访问，首先需要在开发人员机箱与 SQL 托管实例虚拟网络之间建立连接。 为此，请使用本机 Azure 证书身份验证配置与虚拟网络的点到站点连接。 有关详细信息，请参阅[配置点到站点连接，以便从本地计算机连接到 AZURE SQL 托管实例](point-to-site-p2s-configure.md)。

## <a name="connect-with-vnet-peering"></a>与 VNet 对等互连连接

客户实现的另一种方案是，将 VPN 网关安装在单独的虚拟网络中，并从托管 SQL 托管实例的订阅中安装。 然后，将两个虚拟网络对等互连。 下面的示例性体系结构图介绍了实现方法。

![虚拟网络对等互连](./media/connect-application-instance/vnet-peering.png)

设置基本基础结构后，需要修改某些设置，使 VPN 网关能够查看托管 SQL 托管实例的虚拟网络中的 IP 地址。 为此，请在“对等互连设置”下进行下述很具体的更改。****

1. 在托管 VPN 网关的虚拟网络中，请参阅**对等互连**，中转到 SQL 托管实例的对等互连虚拟网络连接，然后单击 "**允许网关传输**"。
2. 在托管 SQL 托管实例的虚拟网络中，请参阅**对等互连**，中转到 VPN 网关的对等互连虚拟网络连接，然后单击 "**使用远程网关**"。

## <a name="connect-azure-app-service"></a>连接 Azure App Service 

你还可以连接 Azure App Service 所承载的应用程序。 SQL 托管实例只能通过专用 IP 地址访问，因此，若要从 Azure App Service 访问它，您首先需要在应用程序与 SQL 托管实例虚拟网络之间建立连接。 请参阅[将应用与 Azure 虚拟网络集成](../../app-service/web-sites-integrate-with-vnet.md)。  

有关疑难解答，请参阅[虚拟网络和应用程序故障排除](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting)。 如果无法建立连接，请尝试[同步网络配置](azure-app-sync-network-configuration.md)。

将 Azure App Service 连接到 SQL 托管实例的一种特殊情况是将 Azure App Service 集成到对等互连到 SQL 托管实例虚拟网络的网络。 对于这种情况，需要设置以下配置：

- SQL 托管实例虚拟网络不能有网关  
- SQL 托管实例虚拟网络必须 `Use remote gateways` 设置选项
- 必须为对等互连虚拟网络 `Allow gateway transit` 设置选项

下图演示了此方案：

![集成的应用对等互连](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>虚拟网络集成功能不会将应用与具有 ExpressRoute 网关的虚拟网络集成。 即使 ExpressRoute 网关是在共存模式下配置的，虚拟网络集成也不起作用。 如果需要通过 ExpressRoute 连接访问资源，可以使用在虚拟网络中运行的应用服务环境。

## <a name="troubleshooting-connectivity-issues"></a>排查连接问题

若要排查连接问题，请查看以下内容：

- 如果无法从同一虚拟网络中的 Azure 虚拟机而不是其他子网连接到 SQL 托管实例，请检查是否在可能阻止访问权限的 VM 子网上设置网络安全组。 此外，打开 SQL 端口1433上的出站连接，以及11000-11999 范围内的端口，因为在 Azure 边界内通过重定向连接需要这些端口。
- 确保已为与虚拟网络关联的路由表将 BGP 传播设置为 "**已启用**"。
- 如果使用 P2S VPN，请在 Azure 门户中检查配置，确定能否看到“入口/出口”编号。**** 如果编号不为零，则表示 Azure 在本地进行流量的出入路由。

   ![入口/出口编号](./media/connect-application-instance/ingress-egress-numbers.png)

- 检查客户端计算机（运行 VPN 客户端）是否包含需要访问的所有虚拟网络的路由条目。 路由存储在 `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` 中。

   ![route.txt](./media/connect-application-instance/route-txt.png)

   如图中所示，每个虚拟网络有两个条目，在门户中配置了 VPN 端点的第三个条目。

   检查路由的另一种方式是执行以下命令。 输出显示到各种子网的路由：

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- 如果你使用的是虚拟网络对等互连，请确保已按照设置[允许网关传输和使用远程网关](#connect-from-on-premises)的说明操作。

- 如果你正在使用虚拟网络对等互连来连接 Azure App Service 托管的应用程序，并且 SQL 托管实例虚拟网络具有公共 IP 地址范围，请确保你的托管应用程序设置允许出站流量路由到公共 IP 网络。 按照[区域虚拟网络集成](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)中的说明进行操作。

## <a name="required-versions-of-drivers-and-tools"></a>所需的驱动程序和工具版本

如果要连接到 SQL 托管实例，建议使用以下最小版本的工具和驱动程序：

| 驱动程序/工具 | Version |
| --- | --- |
|.NET framework | 4.6.1（或 .NET Core） |
|ODBC 驱动程序| v17 |
|PHP 驱动程序| 5.2.0 |
|JDBC 驱动程序| 6.4.0 |
|Node.js 驱动程序| 2.1.1 |
|OLEDB 驱动程序| 18.0.2.0 |
|SSMS| 18.0 或[更高版本](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) 或更高版本 |

## <a name="next-steps"></a>后续步骤

- 有关 SQL 托管实例的信息，请参阅[什么是 sql 托管实例？](sql-managed-instance-paas-overview.md)。
- 有关演示如何创建新的托管实例的教程，请参阅[创建托管实例](instance-create-quickstart.md)。
