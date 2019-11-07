---
title: Azure SQL 数据库和数据仓库网络访问控制
description: 用于管理访问及配置单一数据库或共用数据库的 Azure SQL 数据库和数据仓库网络访问控制的概述。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: deee668c3597047a5d5f0fd153626916c3fd8517
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687711"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL 数据库和数据仓库网络访问控制

> [!NOTE]
> 本文适用于 Azure SQL 服务器，同时也适用于在 Azure SQL 服务器中创建的 SQL 数据库和 SQL 数据仓库数据库。 为简单起见，在提到 SQL 数据库和 SQL 数据仓库时，本文统称 SQL 数据库。

> [!IMPORTANT]
> 本文不适用于 **Azure SQL 数据库托管实例**。 有关网络配置的详细信息，请参阅[连接到托管实例](sql-database-managed-instance-connect-app.md)。

[从 Azure 门户](sql-database-single-database-get-started.md)创建新的 Azure SQL Server 时，结果为*yourservername.database.windows.net*格式的公共终结点。 根据设计，对公共终结点的所有访问都会遭到拒绝。 然后，你可以使用以下网络访问控制来有选择地允许通过公共终结点访问 SQl 数据库
- 允许 Azure 服务：- 设置为“打开”时，Azure 边界范围内的其他资源（例如 Azure 虚拟机）可以访问 SQL 数据库

- IP 防火墙规则：- 使用此功能可以显式允许从特定的 IP 地址（例如，从本地计算机）建立连接。

- 虚拟网络防火墙规则：- 使用此功能可以允许 Azure 边界范围内的特定虚拟网络发来的流量

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>允许 Azure 服务 
[从 Azure 门户](sql-database-single-database-get-started.md)创建新的 Azure SQL Server 期间，此设置将保持未选中状态。

 ![创建新服务器的屏幕截图][1]

创建 Azure SQL Server 之后，也可以按如下所示通过“防火墙”窗格更改此设置。
  
 ![管理服务器防火墙的屏幕截图][2]

设置为“打开”时，Azure SQL Server 将允许 Azure 边界范围内的所有资源（不一定是订阅的一部分）发起的通信。

在许多情况下，“打开”设置的访问权限宽松度会超过大多数客户的需要。他们可能希望将此设置指定为“关闭”，并将其替换为限制性更强的 IP 防火墙规则或虚拟网络防火墙规则。 这会影响以下功能：

### <a name="import-export-service"></a>导入/导出服务

Azure SQL 数据库导入/导出服务在 Azure 中的 VM 上运行。 这些 VM 不在 VNet 中，因此在连接到数据库时会获取 Azure IP。 删除“允许 Azure 服务访问服务器”后，这些 VM 将无法访问数据库。
直接在代码中使用 DACFx API 运行 BACPAC 导入或导出可以解决此问题。

### <a name="sql-database-query-editor"></a>SQL 数据库查询编辑器

Azure SQL 数据库查询编辑器部署在 Azure 中的 VM 上。 这些 VM 不在 VNet 中。 因此，这些 VM 在连接到数据库时会获取 Azure IP。 删除“允许 Azure 服务访问服务器”后，这些 VM 将无法访问数据库。

### <a name="table-auditing"></a>表审核

目前有两种方法可对 SQL 数据库启用审核。 在 Azure SQL Server 上启用服务终结点后，表审核会失败。 在这里，解决办法是转为 Blob 审核。

### <a name="impact-on-data-sync"></a>对数据同步的影响

Azure SQL 数据库具有数据同步功能，可使用 Azure IP 连接到数据库。 使用服务终结点时，将会禁用对 SQL 数据库服务器的“允许 Azure 服务访问服务器”访问权限，并会中断数据同步功能。

## <a name="ip-firewall-rules"></a>IP 防火墙规则
基于 IP 的防火墙是 Azure SQL Server 的一项功能，在显式[添加客户端计算机的 IP 地址](sql-database-server-level-firewall-rule.md)之前，它会阻止对数据库服务器的所有访问。


## <a name="virtual-network-firewall-rules"></a>虚拟网络防火墙规则

除了 IP 规则以外，Azure SQL Server 防火墙还允许定义虚拟网络规则。  
若要了解详细信息，请参阅[AZURE SQL 数据库的虚拟网络服务终结点和规则](sql-database-vnet-service-endpoint-rule-overview.md)或观看此视频：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Azure 网络术语  
在了解虚拟网络防火墙规则时，请注意以下 Azure 网络术语

**虚拟网络：** 你可以具有与你的 Azure 订阅关联的虚拟网络 

**子网:** 虚拟网络包含子网。 你所拥有的任何 Azure 虚拟机 (VM) 都会分配到子网。 一个子网可能包含多个 VM 或其他计算节点。 虚拟网络之外的计算节点不能访问虚拟网络，除非已将安全性配置为允许这样的访问。

**虚拟网络服务终结点：** [虚拟网络服务终结点] [649d] 是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 本文介绍 **Microsoft.Sql** 的类型名称，即名为“SQL 数据库”的 Azure 服务。

虚拟网络规则：适用于 SQL 数据库服务器的虚拟网络规则是一个子网，列在 SQL 数据库服务器的访问控制列表 (ACL) 中。 子网必须包含“Microsoft.Sql”类型名称才会将其列在 SQL 数据库的 ACL 中。 虚拟网络规则要求 SQL 数据库服务器接受来自子网上所有节点的通信。


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 与虚拟网络防火墙规则

在 Azure SQL Server 防火墙中可以指定 IP 地址范围，处于该范围内的通信可以进入 SQL 数据库。 此方法适用于 Azure 专用网络外部的稳定 IP 地址。 但是，对于 Azure 专用网络中的虚拟机 (VM)，将为其配置动态 IP 地址。 当 VM 重启时，动态 IP 地址可能会更改，从而使得基于 IP 的防火墙规则失效。 处于生产环境中时，在防火墙规则中指定一个动态 IP 地址并不明智。

可以通过获取 VM 的静态 IP 地址来解决此限制。 有关详细信息，请参阅 [使用 Azure 门户为虚拟机配置专用 IP 地址][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]。但是，静态 IP 方法可能会变得难以管理，在大规模使用时非常昂贵。 

虚拟网络规则更容易建立，使用此类规则可以更轻松地管理从包含你的 VM 的特定子网进行的访问。

> [!NOTE]
> 目前，子网上没有 SQL 数据库。 如果 Azure SQL 数据库服务器是虚拟网络子网上的一个节点，则虚拟网络中的所有节点都可以与 SQL 数据库通信。 在这种情况下，VM 可以与 SQL 数据库通信，不需任何虚拟网络规则或 IP 规则。

## <a name="next-steps"></a>后续步骤

- 有关创建服务器级 IP 防火墙规则的快速入门，请参阅[创建 Azure SQL 数据库](sql-database-single-database-get-started.md)。

- 有关创建服务器级 VNet 防火墙规则的快速入门，请参阅 [Azure SQL 数据库的虚拟网络服务终结点和规则](sql-database-vnet-service-endpoint-rule-overview.md)。

- 有关从开源或第三方应用程序连接到 Azure SQL 数据库的帮助，请参阅 [SQL 数据库的客户端快速入门代码示例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。

- 有关可能需要打开的其他端口的信息，请参阅**用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口**中的 [SQL 数据库：外部与内部](sql-database-develop-direct-route-ports-adonet-v12.md)部分

- 有关 Azure SQL 数据库的连接概述，请参阅 [Azure SQL 连接体系结构](sql-database-connectivity-architecture.md)

- 有关 Azure SQL 数据库安全概述，请参阅[保护数据库](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
