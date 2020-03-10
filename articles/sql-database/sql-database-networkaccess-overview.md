---
title: 网络访问控件
description: 概述用于管理访问权限的 Azure SQL 数据库和数据仓库的网络访问控制，以及配置单个或共用的数据库。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 822fab5c00501d415c3c184587141e869523e417
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945384"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL 数据库和数据仓库网络访问控制

> [!NOTE]
> 本文适用于 Azure SQL 服务器，同时也适用于在 Azure SQL 服务器中创建的 SQL 数据库和 SQL 数据仓库数据库。 为简单起见，在提到 SQL 数据库和 SQL 数据仓库时，本文统称 SQL 数据库。

> [!IMPORTANT]
> 本文不适用于 **Azure SQL 数据库托管实例**。 有关网络配置的详细信息，请参阅[连接到托管实例](sql-database-managed-instance-connect-app.md)。

从[Azure 门户](sql-database-single-database-get-started.md)创建新的 Azure SQL Server 时，结果为*yourservername.database.windows.net*格式的公共终结点。

你可以使用以下网络访问控制来有选择地允许通过公共终结点访问 SQl 数据库：
- 允许 Azure 服务：当设置为 ON 时，Azure 边界内的其他资源（例如 Azure 虚拟机）可以访问 SQL 数据库

- IP 防火墙规则：使用此功能显式允许来自特定 IP 地址的连接，例如来自本地计算机的连接

还可以通过以下方式允许从[虚拟网络](../virtual-network/virtual-networks-overview.md)对 SQL 数据库进行专用访问：
- 虚拟网络防火墙规则：使用此功能允许来自 Azure 边界内特定虚拟网络的流量

- 专用链接：使用此功能可在特定虚拟网络中为 Azure SQL Server 创建专用终结点



请参阅下面的视频，详细了解这些访问控制及其功能：
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>允许 Azure 服务 
在[从 Azure 门户](sql-database-single-database-get-started.md)创建新的 Azure SQL Server 期间，此设置将处于未选中状态。



你还可以在创建 Azure SQL Server 之后通过 "防火墙" 窗格更改此设置，如下所示。
  
 ![管理服务器防火墙的屏幕截图][2]

当设置为**ON** azure SQL Server 允许来自 Azure 边界内的所有资源（可能是订阅的一部分）的通信。

在许多情况下，"**开启**" 设置比大多数客户所需的更多。他们可能想要将此设置设置为 "**关闭**"，并将其替换为限制性更强的 IP 防火墙规则或虚拟网络防火墙规则。 这样做会影响在 Azure 中不属于 VNet 的 Vm 上运行的以下功能，并因此通过 Azure IP 地址连接到 Sql 数据库。

### <a name="import-export-service"></a>导入/导出服务
导入导出服务不起作用**允许 Azure 服务访问服务器**设置为关闭。 不过，可以[通过在 AZURE VM 中手动运行 sqlpackage 或](https://docs.microsoft.com/azure/sql-database/import-export-from-vm)使用 DACFx API 直接在代码中执行导出，来解决此问题。

### <a name="data-sync"></a>数据同步
若要将数据同步功能与 "**允许 Azure 服务访问服务器**" 设置为 "关闭"，需要创建单个防火墙规则条目，以便为托管**中心**数据库的区域的**Sql 服务标记**[添加 IP 地址](sql-database-server-level-firewall-rule.md)。
将这些服务器级防火墙规则添加到托管**中心**和**成员**数据库的逻辑服务器（可能位于不同区域）

使用以下 PowerShell 脚本生成对应于美国西部区域的 Sql 服务标记的 IP 地址
```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> 尽管指定 Location 参数，但 AzNetworkServiceTag 返回 Sql 服务标记的全局范围。 请确保将其筛选到承载同步组所使用的中心数据库的区域

请注意，PowerShell 脚本的输出是无类域间路由（CIDR）表示法，需要使用[Get-IPrangeStartEnd](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9)将其转换为起始和结束 IP 地址的格式，如下所示
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

执行以下附加步骤，将所有 IP 地址从 CIDR 转换为开始和结束 IP 地址格式。

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
你现在可以将其添加为不同的防火墙规则，然后将 "**允许 Azure 服务访问服务器**" 设置为 "关闭"。


## <a name="ip-firewall-rules"></a>IP 防火墙规则
基于 Ip 的防火墙是 Azure SQL Server 的一项功能，可阻止对数据库服务器的所有访问，直到显式添加客户端计算机的[IP 地址](sql-database-server-level-firewall-rule.md)。


## <a name="virtual-network-firewall-rules"></a>虚拟网络防火墙规则

除了 IP 规则，Azure SQL Server 防火墙还允许你定义*虚拟网络规则*。  
若要了解详细信息，请参阅[AZURE SQL 数据库的虚拟网络服务终结点和规则](sql-database-vnet-service-endpoint-rule-overview.md)或观看此视频：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Azure 网络术语  
在探索虚拟网络防火墙规则时，请注意以下 Azure 网络术语

**虚拟网络：** 你可以具有与你的 Azure 订阅关联的虚拟网络 

子网：虚拟网络包含子网。 你所拥有的任何 Azure 虚拟机 (VM) 都会分配到子网。 一个子网可能包含多个 VM 或其他计算节点。 虚拟网络之外的计算节点不能访问虚拟网络，除非已将安全性配置为允许这样的访问。

虚拟网络服务终结点：[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 本文介绍 **Microsoft.Sql** 的类型名称，即名为“SQL 数据库”的 Azure 服务。

虚拟网络规则：适用于 SQL 数据库服务器的虚拟网络规则是一个子网，列在 SQL 数据库服务器的访问控制列表 (ACL) 中。 子网必须包含“Microsoft.Sql”类型名称才会将其列在 SQL 数据库的 ACL 中。 虚拟网络规则要求 SQL 数据库服务器接受来自子网上所有节点的通信。


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 与虚拟网络防火墙规则

利用 Azure SQL Server 防火墙，你可以指定 IP 地址范围，从该范围内可接受到 SQL 数据库的通信。 此方法适用于 Azure 专用网络外部的稳定 IP 地址。 但是，Azure 专用网络中的虚拟机（Vm）配置有*动态*IP 地址。 当 VM 重新启动时，动态 IP 地址可能会更改，因此会使基于 IP 的防火墙规则无效。 处于生产环境中时，在防火墙规则中指定一个动态 IP 地址并不明智。

可以通过获取 VM 的*静态*IP 地址来解决此限制。 有关详细信息，请参阅[使用 Azure 门户为虚拟机配置专用 IP 地址](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)。 但是，静态 IP 方法可能会变得难以管理，在规模大时操作成本高。 

虚拟网络规则是建立和管理来自包含 Vm 的特定子网的访问权限的简便方法。

> [!NOTE]
> 目前，子网上没有 SQL 数据库。 如果 Azure SQL 数据库服务器是虚拟网络子网上的一个节点，则虚拟网络中的所有节点都可以与 SQL 数据库通信。 在这种情况下，VM 可以与 SQL 数据库通信，不需任何虚拟网络规则或 IP 规则。

## <a name="private-link"></a>专用链接 
专用链接允许通过**专用终结点**连接到 Azure SQL Server。 专用终结点是特定[虚拟网络](../virtual-network/virtual-networks-overview.md)和子网中的专用 IP 地址。

## <a name="next-steps"></a>后续步骤

- 有关创建服务器级 IP 防火墙规则的快速入门，请参阅[创建 AZURE SQL 数据库](sql-database-single-database-get-started.md)。

- 有关创建服务器级 Vnet 防火墙规则的快速入门，请参阅[AZURE SQL 数据库的虚拟网络服务终结点和规则](sql-database-vnet-service-endpoint-rule-overview.md)。

- 有关从开放源代码或第三方应用程序连接到 Azure SQL 数据库的帮助，请参阅[SQL 数据库的客户端快速入门代码示例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。

- 有关可能需要打开的其他端口的信息，请参阅**用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口**中的 [SQL 数据库：外部与内部](sql-database-develop-direct-route-ports-adonet-v12.md)部分

- 有关 Azure SQL 数据库连接的概述，请参阅[AZURE Sql 连接体系结构](sql-database-connectivity-architecture.md)

- 有关 Azure SQL 数据库安全概述，请参阅[保护数据库](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

