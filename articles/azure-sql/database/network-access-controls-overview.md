---
title: 网络访问控件
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: 概述如何管理和控制 Azure SQL 数据库和 Azure Synapse Analytics （以前称为 Azure SQL 数据仓库）的网络访问。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 435a5fe6f5900ffe742d4459e8e402d2e698ca9f
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085457"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL 数据库和 Azure Synapse Analytics 网络访问控制

如果从 Azure SQL 数据库和 Azure Synapse Analytics [Azure 门户](single-database-create-quickstart.md)创建逻辑 SQL server，则结果为*yourservername.database.windows.net*格式的公共终结点。

你可以使用以下网络访问控制来有选择地允许通过公共终结点访问数据库：

- 允许 Azure 服务：设置为“打开”时，Azure 边界内的其他资源（例如 Azure 虚拟机）可以访问 SQL 数据库
- IP 防火墙规则：使用此功能可以显式允许从特定的 IP 地址（例如，从本地计算机）建立连接

还可以通过以下方式允许从[虚拟网络](../../virtual-network/virtual-networks-overview.md)对数据库进行私有访问：

- 虚拟网络防火墙规则：使用此功能允许来自 Azure 边界内特定虚拟网络的流量
- 专用链接：使用此功能可为特定虚拟网络中的[逻辑 SQL server](logical-servers.md)创建专用终结点

> [!IMPORTANT]
> 本文*不适用于* **SQL 托管实例**。 有关网络配置的详细信息，请参阅[连接到 AZURE SQL 托管实例](../managed-instance/connect-application-instance.md)。

请参阅下面的视频，详细了解这些访问控制及其功能：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>允许 Azure 服务

在[从 Azure 门户](single-database-create-quickstart.md)创建新的逻辑 SQL server 期间，此设置将处于未选中状态。

你还可以在创建逻辑 SQL server 后通过 "防火墙" 窗格更改此设置，如下所示。
  
![管理服务器防火墙的屏幕截图][2]

当设置为 **"打开**" 时，你的服务器允许来自 Azure 边界内的所有资源的通信，这些资源可能是订阅的一部分，也可能不是。

在许多情况下，“打开”设置的访问权限宽松度会超过大多数客户的需要****。 你可能想要将此设置设置为 "**关闭**"，并将其替换为限制性更强的 IP 防火墙规则或虚拟网络防火墙规则。 

不过，这样做会影响在 Azure 中的虚拟机上运行的以下功能，这些功能不是虚拟网络的一部分，因此可以通过 Azure IP 地址连接到数据库：

### <a name="import-export-service"></a>导入/导出服务

当 "**允许访问 Azure 服务**" 设置为 "**关闭**" 时，导入导出服务不起作用。 不过，可通过以下方式解决此问题：[在 Azure VM 中手动运行 sqlpackage.exe，或者直接在代码中使用 DACFx API 执行导出](https://docs.microsoft.com/azure/sql-database/import-export-from-vm)。

### <a name="data-sync"></a>数据同步

若要在“允许访问 Azure 服务”设置为“关闭”的情况下使用数据同步功能，需要创建单个防火墙规则条目，以便从托管中心**** 数据库的区域的 SQL 服务标记****[添加 IP 地址](firewall-create-server-level-portal-quickstart.md)。**** ****
将这些服务器级防火墙规则添加到托管**中心**和**成员**数据库的服务器（可能位于不同区域）

使用以下 PowerShell 脚本生成对应于美国西部区域的 SQL 服务标记的 IP 地址

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
> 即使指定 Location 参数，Get-AzNetworkServiceTag 也会返回 SQL 服务标记的全局范围。 请务必将范围筛选为托管同步组所用中心数据库的区域

请注意，PowerShell 脚本的输出采用无类别域间路由（CIDR）表示法。 这需要使用如下所示的[Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9)转换为开始和结束 IP 地址的格式：

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

你可以使用此附加 PowerShell 脚本将所有 IP 地址从 CIDR 转换为起始和结束 IP 地址格式。

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

现在，可将其添加为不同的防火墙规则，然后将“允许 Azure 服务访问服务器”设置为“关闭”。****

## <a name="ip-firewall-rules"></a>IP 防火墙规则

基于 Ip 的防火墙是 Azure 中逻辑 SQL server 的一项功能，它可以阻止对服务器的所有访问，直到显式添加客户端计算机的[IP 地址](firewall-create-server-level-portal-quickstart.md)。

## <a name="virtual-network-firewall-rules"></a>虚拟网络防火墙规则

除了 IP 规则，服务器防火墙还允许您定义*虚拟网络规则*。  
若要了解详细信息，请参阅[AZURE SQL 数据库的虚拟网络服务终结点和规则](vnet-service-endpoint-rule-overview.md)或观看此视频：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Azure 网络术语

在探索虚拟网络防火墙规则时，请注意以下 Azure 网络术语

**虚拟网络：** 可以让虚拟网络与 Azure 订阅相关联

**子网：** 虚拟网络包含**子网**。 你所拥有的任何 Azure 虚拟机 (VM) 都会分配到子网。 一个子网可能包含多个 VM 或其他计算节点。 虚拟网络外部的计算节点无法访问你的虚拟网络，除非你将安全性配置为允许访问。

**虚拟网络服务终结点：**[虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 在本文中，我们对**Microsoft .sql**的类型名称感兴趣，该名称引用名为 Sql Database 的 Azure 服务。

**虚拟网络规则：** 服务器的虚拟网络规则是在服务器的访问控制列表（ACL）中列出的子网。 若要在 SQL 数据库中的数据库的 ACL 中，子网必须包含 " **sql** " 类型名称。 虚拟网络规则告诉服务器接受来自子网中的每个节点的通信。

## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 与虚拟网络防火墙规则

通过 Azure SQL 数据库防火墙，你可以指定 IP 地址范围，从该地址范围内可接受到 SQL 数据库的通信。 此方法适用于 Azure 专用网络外部的稳定 IP 地址。 但是，对于 Azure 专用网络中的虚拟机 (VM)，将为其配置动态 IP 地址。** 当 VM 重启时，动态 IP 地址可能会更改，从而使得基于 IP 的防火墙规则失效。 处于生产环境中时，在防火墙规则中指定一个动态 IP 地址并不明智。

可以通过获取 VM 的静态 IP 地址来解决此限制。** 有关详细信息，请参阅[使用 Azure 门户创建具有静态公共 IP 地址的虚拟机](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md)。 但是，静态 IP 方法可能会变得难以管理，并且规模非常昂贵。

虚拟网络规则更容易建立，使用此类规则可以更轻松地管理从包含你的 VM 的特定子网进行的访问。

> [!NOTE]
> 目前，子网上不允许有 SQL 数据库。 如果服务器是虚拟网络中子网的节点，则虚拟网络中的所有节点都可以与 SQL 数据库通信。 在这种情况下，VM 可以与 SQL 数据库通信，不需任何虚拟网络规则或 IP 规则。

## <a name="private-link"></a>专用链接

专用链接允许通过**专用终结点**连接到服务器。 专用终结点是特定[虚拟网络](../../virtual-network/virtual-networks-overview.md)和子网中的专用 IP 地址。

## <a name="next-steps"></a>后续步骤

- 有关创建服务器级 IP 防火墙规则的快速入门，请参阅[在 SQL 数据库中创建数据库](single-database-create-quickstart.md)。

- 有关创建服务器级虚拟网络防火墙规则的快速入门，请参阅[AZURE SQL 数据库的虚拟网络服务终结点和规则](vnet-service-endpoint-rule-overview.md)。

- 有关从开放源代码或第三方应用程序连接到 SQL 数据库中的数据库的帮助，请参阅[Sql 数据库的客户端快速入门代码示例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。

- 有关可能需要打开的其他端口的信息，请参阅 **SQL 数据库：外部与内部**部分（在[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](adonet-v12-develop-direct-route-ports.md)中）

- 有关 Azure SQL 数据库的连接概述，请参阅 [Azure SQL 连接体系结构](connectivity-architecture.md)

- 有关 Azure SQL 数据库安全概述，请参阅[保护数据库](security-overview.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
 
