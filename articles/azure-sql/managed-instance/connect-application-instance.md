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
ms.reviewer: sstein, bonova, vanto
ms.date: 11/09/2018
ms.openlocfilehash: a59e498435aab7b3e3e2ecf2e6096c044550a1b8
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628360"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>将应用程序连接到 Azure SQL 托管实例
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

在确定如何以及在何处托管应用程序时，目前有多个选项可供选择。

可选择使用 Azure 应用服务或 Azure 的某些虚拟网络集成选项（例如 Azure 应用服务环境、Azure 虚拟机和虚拟机规模集）将应用程序托管在云中。 还可采用混合云方法将应用程序保留在本地。

不管选择哪个选项，都可将应用程序连接到 Azure SQL 托管实例。 

![高可用性](./media/connect-application-instance/application-deployment-topologies.png)

本文介绍如何在众多不同的应用方案中将应用程序连接到 Azure SQL 托管实例。 

## <a name="connect-inside-the-same-vnet"></a>在同一 VNet 中连接

最简单的方案是将应用程序连接到 Azure SQL 托管实例所在的虚拟网络中。 虚拟网络中的虚拟机即使位于不同子网，也能直接相互连接。 这意味着，只需相应地设置连接字符串，就能连接应用服务环境或虚拟机中的应用程序。  

## <a name="connect-inside-a-different-vnet"></a>在不同的 VNet 中连接

当应用程序位于与 SQL 托管实例不同的虚拟网络中时，连接应用程序要稍微复杂一些，因为 SQL 托管实例在其自己的虚拟网络中具有专用 IP 地址。 若要建立连接，应用程序需要访问部署了 SQL 托管实例的虚拟网络。 因此需要在应用程序与 SQL 托管实例虚拟网络之间建立连接。 要使该方案可行，不一定要将虚拟网络置于同一订阅中。

可通过两个选项连接虚拟网络：

- [Azure VNet 对等互连](../../virtual-network/virtual-network-peering-overview.md)
- VNet 到 VNet VPN 网关（[Azure 门户](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)、[PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)、[Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)）

对等互连使用 Microsoft 主干网络，因此是首选；这样的话，从连接角度来看，对等互连虚拟网络中的虚拟机与同一虚拟网络中虚拟机之间的延迟没有明显差别。 同一区域中的网络之间支持虚拟网络对等互连。 还支持全局虚拟网络对等互连，同时提供以下说明中所述的限制。  

> [!IMPORTANT]
> [在9/22/2020 中，我们为新创建的虚拟群集宣布了全局虚拟网络对等互连](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/)。 这意味着，对于在公告日期之后在空子网中创建的 SQL 托管实例，以及在这些子网中创建的所有后续托管实例，都支持全局虚拟网络对等互连。 对于所有其他 SQL 托管实例的对等互连，都将限制为同一区域的网络，因为 [全局虚拟网络对等互连的限制](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)。 有关更多详细信息，另请参阅 [Azure 虚拟网络常见问题解答](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)一文的相关部分。 

## <a name="connect-from-on-premises"></a>从本地连接 

还可将本地应用程序连接到 SQL 托管实例。 只能通过专用 IP 地址访问 SQL 托管实例。 若要从本地访问它，需在应用程序与 SQL 托管实例虚拟网络之间建立站点到站点连接。

可使用两个选项从本地连接到 Azure 虚拟网络：

- 站点到站点 VPN 连接（[Azure 门户](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)、[PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)、[Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)）
- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) 连接  

如果已成功建立本地到 Azure 的连接，但无法与 SQL 托管实例建立连接，请检查防火墙中是否在 SQL 端口 1433 上打开了出站连接，并确认已打开 11000-11999 范围的端口来进行重定向。

## <a name="connect-the-developer-box"></a>连接开发人员工具箱

还可将开发人员工具箱连接到 SQL 托管实例。 只能通过专用 IP 地址访问 SQL 托管实例，因此若要从开发人员工具箱访问该实例，首先需要在开发人员工具箱与 SQL 托管实例虚拟网络之间建立连接。 为此，请使用本机 Azure 证书身份验证配置与虚拟网络的点到站点连接。 有关详细信息，请参阅[配置点到站点连接，以便从本地计算机连接到 Azure SQL 托管实例](point-to-site-p2s-configure.md)。

## <a name="connect-with-vnet-peering"></a>通过 VNet 对等互连进行连接

在通过客户实现的另一方案中，VPN 网关安装在与承载 SQL 托管实例不同的虚拟网络和订阅中。 然后，将这两个虚拟网络对等互连。 下面的示例性体系结构图介绍了实现方法。

![虚拟网络对等互连](./media/connect-application-instance/vnet-peering.png)

设置基本的体系结构后，需修改某些设置，使 VPN 网关能够看到承载 SQL 托管实例的虚拟网络中的 IP 地址。 为此，请在“对等互连设置”下进行下述很具体的更改。

1. 在承载 VPN 网关的虚拟网络中，转到“对等互连”，然后转到 SQL 托管实例的对等虚拟网络连接，再单击“允许网关传输” 。
2. 在承载 SQL 托管实例的虚拟网络中，转到“对等互连”，然后转到 VPN 网关的对等虚拟网络连接，再单击“使用远程网关” 。

## <a name="connect-azure-app-service"></a>连接 Azure 应用服务 

还可连接由 Azure 应用服务托管的应用程序。 只能通过专用 IP 地址访问 SQL 托管实例，因此若要从 Azure 应用服务访问该实例，首先需要在应用程序与 SQL 托管实例虚拟网络之间建立连接。 请参阅[将应用与 Azure 虚拟网络集成](../../app-service/web-sites-integrate-with-vnet.md)。  

有关故障排除信息，请参阅[排查虚拟网络和应用程序问题](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting)。 如果无法建立连接，请尝试[同步网络配置](azure-app-sync-network-configuration.md)。

在将 Azure 应用服务连接到 SQL 托管实例时，有一种特殊情况是将 Azure 应用服务集成到已与 SQL 托管实例虚拟网络建立对等互连的网络。 对于这种情况，需要设置以下配置：

- SQL 托管实例虚拟网络不得使用网关  
- SQL 托管实例虚拟网络必须设置 `Use remote gateways` 选项
- 对等的虚拟网络必须设置 `Allow gateway transit` 选项

下图演示了此方案：

![集成的应用对等互连](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>虚拟网络集成功能不会将应用与包含 ExpressRoute 网关的虚拟网络集成。 即使 ExpressRoute 网关是以共存模式配置的，也无法实现虚拟网络集成。 如果需要通过 ExpressRoute 连接访问资源，可使用虚拟网络中运行的应用服务环境。

## <a name="troubleshooting-connectivity-issues"></a>排查连接问题

若要排查连接问题，请查看以下内容：

- 如果无法从同一虚拟网络的不同子网中的 Azure 虚拟机连接到 SQL 托管实例，请检查 VM 子网上是否设置了可能会阻止访问的网络安全组。 此外，请在 SQL 端口 1433 上和 11000-11999 范围的端口上打开出站连接，因为需要使用它们才能在 Azure 边界内通过重定向进行连接。
- 对于与虚拟网络关联的路由表，请确保将“BGP 传播”设置为“启用”。
- 如果使用 P2S VPN，请在 Azure 门户中检查配置，确定能否看到“入口/出口”编号。 如果编号不为零，则表示 Azure 在本地进行流量的出入路由。

   ![入口/出口编号](./media/connect-application-instance/ingress-egress-numbers.png)

- 查看客户端计算机（运行 VPN 客户端的计算机）是否针对你需要访问的所有虚拟网络设置了路由条目。 路由存储在 `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` 中。

   ![route.txt](./media/connect-application-instance/route-txt.png)

   如此图所示，有两个条目用于所涉及的每个虚拟网络，还有第三个条目与门户中配置的 VPN 终结点相对应。

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

- 如果使用虚拟网络对等互连，请确保已按照相关说明来设置[允许网关传输并使用远程网关](#connect-from-on-premises)。

- 如果使用虚拟网络对等互连连接 Azure 应用服务托管应用程序，而且 SQL 托管实例虚拟网络具有公共 IP 地址范围，请确保你的托管应用程序设置允许出站流量路由到公共 IP 网络。 按照 [区域虚拟网络集成](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)中的说明进行操作。

## <a name="required-versions-of-drivers-and-tools"></a>所需的驱动程序和工具版本

如果要连接到 SQL 托管实例，建议使用以下最低版本的工具和驱动程序：

| 驱动程序/工具 | 版本 |
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

- 有关 SQL 托管实例的信息，请参阅[什么是 SQL 托管实例？](sql-managed-instance-paas-overview.md)。
- 有关演示如何新建托管实例的教程，请参阅[创建托管实例](instance-create-quickstart.md)。
