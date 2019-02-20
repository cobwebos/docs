---
title: Azure SQL 数据库和数据仓库 IP 防火墙规则 | Microsoft Docs
description: 了解如何为 SQL 数据库或 SQL 数据仓库防火墙配置可管理访问权限的服务器级别 IP 防火墙规则，以及如何为单一数据库或入池数据库配置数据库级别 IP 防火墙规则。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: dcd0c7073f2126e001a65e2142ea54a229553ebd
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894694"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL 数据库和 SQL 数据仓库 IP 防火墙规则

Microsoft Azure [SQL 数据库](sql-database-technical-overview.md)和 [SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)为 Azure 和其他基于 Internet 的应用程序提供关系型数据库服务。 为了保护数据，在指定哪些计算机具有访问权限之前，防火墙将禁止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。

> [!NOTE]
> 本文适用于 Azure SQL 服务器，同时也适用于在 Azure SQL 服务器中创建的 SQL 数据库和 SQL 数据仓库数据库。 为简单起见，在提到 SQL 数据库和 SQL 数据仓库时，本文统称 SQL 数据库。
> [!IMPORTANT]
> 本文不适用于 **Azure SQL 数据库托管实例**。 有关所需网络配置的详细信息，请参阅以下有关[连接到托管实例](sql-database-managed-instance-connect-app.md)的文章。

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>将虚拟网络规则作为 IP 规则的替代

除了 IP 规则外，防火墙还管理*虚拟网络规则*。 虚拟网络规则基于虚拟网络服务终结点。 在某些情况下，虚拟网络规则可能比 IP 规则更好。 若要了解详细信息，请参阅[虚拟网络服务终结点和 Azure SQL 数据库规则](sql-database-vnet-service-endpoint-rule-overview.md)。

## <a name="overview"></a>概述

最初，SQL 数据库防火墙会阻止对 Azure SQL 数据库的所有访问。 必须指定一个或多个允许访问 Azure SQL 服务器的服务器级别 IP 防火墙规则，才能访问数据库服务器。 使用 IP 防火墙规则可以指定允许的 Internet IP 地址范围，以及 Azure 应用程序能否尝试连接到 Azure SQL 服务器。

若要有选择地授予对 Azure SQL Server 中的一个数据库的访问权限，必须针对所需的数据库创建数据库级规则。 请指定超出服务器级别 IP 防火墙规则中指定 IP 地址范围的数据库 IP 防火墙规则 IP 地址范围，并确保客户端的 IP 地址在数据库级别规则中指定的范围内。

> [!IMPORTANT]
> SQL 数据仓库仅支持服务器级别 IP 防火墙规则，不支持数据库级别 IP 防火墙规则。

来自 Internet 和 Azure 的连接尝试必须首先通过防火墙，才能访问 Azure SQL Server 或 SQL 数据库，如下图中所示：

   ![描述防火墙配置的示意图。][1]

- **服务器级别 IP 防火墙规则：**

  这些规则允许客户端访问整个 Azure SQL 服务器，即同一 SQL 数据库服务器内的所有数据库。 这些规则存储在 **master** 数据库中。 可使用门户或 Transact-SQL 语句来配置服务器级别 IP 防火墙规则。 用户必须是订阅所有者或订阅参与者，才能使用 Azure 门户或 PowerShell 创建服务器级别 IP 防火墙规则。 用户必须以服务器级别主体登录名或 Azure Active Directory 管理员的身份连接到 SQL 数据库实例（也就是说，必须先由拥有 Azure 级别权限的用户创建服务器级别 IP 防火墙规则），才能使用 Transact-SQL 创建服务器级别 IP 防火墙规则。

- **数据库级别 IP 防火墙规则：**

  这些规则允许客户端访问同一 SQL 数据库服务器内的某些（安全）数据库。 可以为每个数据库创建这些规则（包括 master 数据库），它们将存储在单独的数据库中。 只有在配置了第一个服务器级别防火墙后，才只能使用 Transact-SQL 语句创建和管理用于 master 数据库和用户数据库的数据库级别 IP 防火墙规则。 如果在数据库级别 IP 防火墙规则中指定的 IP 地址范围超出了在服务器级别 IP 防火墙规则中指定的范围，只有 IP 地址处于数据库级别范围内的客户端才能访问数据库。 对于每个数据库，最多可以有 128 个数据库级别 IP 防火墙规则。 若要详细了解如何配置数据库级别 IP 防火墙规则，请参阅本文后面部分中的示例，以及 [sp_set_database_firewall_rule（Azure SQL 数据库）](https://msdn.microsoft.com/library/dn270010.aspx)。

### <a name="recommendation"></a>建议

Microsoft 建议尽量使用数据库级别 IP 防火墙规则，以增强安全性并提高数据库的可移植性。 如果有多个访问要求相同的数据库，且不希望花时间来分别配置每个数据库，请使用面向管理员的服务器级别 IP 防火墙规则。

> [!IMPORTANT]
> Microsoft Azure SQL 数据库最多支持 128 个 IP 防火墙规则。
> [!NOTE]
> 有关业务连续性上下文中的可移植数据库的信息，请参阅[灾难恢复的身份验证要求](sql-database-geo-replication-security-config.md)。

### <a name="connecting-from-the-internet"></a>从 Internet 连接

在计算机尝试从 Internet 连接到数据库服务器时，防火墙先针对请求连接的数据库，根据数据库级别 IP 防火墙规则来检查请求的发起 IP 地址：

- 如果请求的 IP 地址在数据库级别 IP 防火墙规则中指定的范围之一内，包含规则的 SQL 数据库便会获得连接授权。
- 如果请求的 IP 地址不在数据库级别 IP 防火墙规则中指定的范围之一内，便会检查服务器级别 IP 防火墙规则。 如果请求的 IP 地址在服务器级别 IP 防火墙规则中指定的范围之一内，便会进行连接授权。 服务器级别 IP 防火墙规则适用于 Azure SQL 服务器上的所有 SQL 数据库。  
- 如果请求的 IP 地址不在任何数据库级别或服务器级别 IP 防火墙规则中指定的范围内，连接请求失败。

> [!NOTE]
> 要从本地计算机访问 Azure SQL 数据库，请确保网络和本地计算机上的防火墙允许在 TCP 端口 1433 上的传出通信。

### <a name="connecting-from-azure"></a>从 Azure 连接

若要允许来自 Azure 的应用程序连接到 Azure SQL Server，则必须启用 Azure 连接。 在应用程序尝试从 Azure 连接到数据库服务器时，防火墙会验证是否允许 Azure 连接。 如果防火墙设置的开始地址和结束地址都等于 0.0.0.0，表明允许 Azure 连接。 如果不允许该连接尝试，则该请求将不会访问 Azure SQL 数据库服务器。

> [!IMPORTANT]
> 该选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问权限限制为仅已授权用户使用。

## <a name="creating-and-managing-ip-firewall-rules"></a>创建和管理 IP 防火墙规则

第一个服务器级防火墙设置可以使用 [Azure 门户](https://portal.azure.com/)进行创建，也可以使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql)、[Azure CLI](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) 或 [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) 通过编程方式创建。 可以使用这些方法或通过 Transact-SQL 创建和管理后续的服务器级别 IP 防火墙规则。

> [!IMPORTANT]
> 只能使用 Transact-SQL 创建和管理数据库级别 IP 防火墙规则。

为了提升性能，服务器级别 IP 防火墙规则暂时在数据库级别缓存。 若要刷新高速缓存，请参阅 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)。

> [!TIP]
> 可以使用[SQL 数据库审核](sql-database-auditing.md)来审核服务器级别和数据库级别防火墙的更改。

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>使用 Azure 门户管理服务器级别 IP 防火墙规则

若要在 Azure 门户中设置服务器级别 IP 防火墙规则，可以转到 Azure SQL 数据库的“概览”页或 SQL 数据库服务器的“概览”页。

> [!TIP]
> 有关教程，请参阅[使用 Azure 门户创建 DB](sql-database-single-database-get-started.md)。

### <a name="from-database-overview-page"></a>从数据库概述页

1. 若要在数据库概述页中设置服务器级别 IP 防火墙规则，请单击工具栏上的“设置服务器防火墙”，如下图所示：此时会打开 SQL 数据库服务器的“防火墙设置”页。

      ![服务器 IP 防火墙规则](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. 单击工具栏上的“添加客户端 IP”以添加当前使用的计算机的 IP 地址，并单击“保存”。 此时，系统针对当前 IP 地址创建服务器级别 IP 防火墙规则。

      ![设置服务器级别 IP 防火墙规则](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>从服务器概述页

此时会打开服务器的概述页，其中显示了完全限定的服务器名称（例如 **mynewserver20170403.database.windows.net**），并提供了其他配置的选项。

1. 若要从服务器概述页设置服务器级规则，请在“设置”下方单击左侧菜单中的“防火墙”：

2. 单击工具栏上的“添加客户端 IP”以添加当前使用的计算机的 IP 地址，并单击“保存”。 此时，系统针对当前 IP 地址创建服务器级别 IP 防火墙规则。

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>使用 Transact-SQL 管理 IP 防火墙规则

| 目录视图或存储过程 | 级别 | 说明 |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |服务器 |显示当前服务器级别 IP 防火墙规则 |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |服务器 |创建或更新服务器级别 IP 防火墙规则 |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |服务器 |删除服务器级别 IP 防火墙规则 |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |数据库 |显示当前数据库级别 IP 防火墙规则 |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |数据库 |创建或更新数据库级别 IP 防火墙规则 |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |数据库 |删除数据库级别 IP 防火墙规则 |

下面的示例检查现有规则，在服务器 Contoso 上启用一系列 IP 地址，并删除 IP 防火墙规则：

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

接下来，添加服务器级别 IP 防火墙规则。

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

若要删除服务器级别 IP 防火墙规则，请执行 sp_delete_firewall_rule 存储过程。 以下示例将删除名为 ContosoFirewallRule 的规则：

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>使用 Azure PowerShell 管理服务器级别 IP 防火墙规则

| Cmdlet | 级别 | 说明 |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |服务器 |返回当前服务器级别 IP 防火墙规则 |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |服务器 |新建服务器级别 IP 防火墙规则 |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |服务器 |更新现有服务器级别 IP 防火墙规则的属性 |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |服务器 |删除服务器级别 IP 防火墙规则 |

下面的示例使用 PowerShell 设置服务器级别 IP 防火墙规则：

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> 若要查看快速入门上下文中的 PowerShell 示例，请参阅[创建 DB - PowerShell](sql-database-powershell-samples.md)，以及[使用 PowerShell 创建单一数据库并配置 SQL 数据库服务器级别 IP 防火墙规则](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>使用 Azure CLI 管理服务器级别 IP 防火墙规则

| Cmdlet | 级别 | 说明 |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|服务器|创建服务器 IP 防火墙规则|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|服务器|列出服务器上的 IP 防火墙规则|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|服务器|显示 IP 防火墙规则的详细信息|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|服务器|更新 IP 防火墙规则|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|服务器|删除 IP 防火墙规则|

下面的示例使用 Azure CLI 设置服务器级别 IP 防火墙规则：

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> 若要查看快速入门上下文中的 Azure CLI 示例，请参阅[创建 DB - Azure CLI](sql-database-cli-samples.md)，以及[使用 Azure CLI 创建单一数据库并配置 SQL 数据库 IP 防火墙规则](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>使用 REST API 管理服务器级别 IP 防火墙规则

| API | 级别 | 说明 |
| --- | --- | --- |
| [列出防火墙规则](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |服务器 |显示当前服务器级别 IP 防火墙规则 |
| [创建或更新防火墙规则](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |服务器 |创建或更新服务器级别 IP 防火墙规则 |
| [删除防火墙规则](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |服务器 |删除服务器级别 IP 防火墙规则 |
| [获取防火墙规则](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | 服务器 | 获取服务器级别 IP 防火墙规则 |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>服务器级别与数据库级别 IP 防火墙规则

问： 是否应将一个数据库的用户与另一个数据库完全隔离？
如果是，使用数据库级别 IP 防火墙规则授予访问权限。 这样可以避免使用服务器级别 IP 防火墙规则（此规则允许通过防火墙访问所有数据库，进而降低防御程度）。

问： IP 地址用户是否需要访问所有数据库？
使用服务器级别 IP 防火墙规则可减少必须配置 IP 防火墙规则的次数。

问： 配置 IP 防火墙规则的个人或团队是否只能通过 Azure 门户、PowerShell 或 REST API 获取访问权限？
必须使用服务器级别 IP 防火墙规则。 只能使用 Transact-SQL 配置数据库级别 IP 防火墙规则。  

问： 是否禁止配置 IP 防火墙规则的个人或团队在数据库级别拥有高级权限？
使用服务器级别 IP 防火墙规则。 如果使用 Transact-SQL 配置数据库级别 IP 防火墙规则，在数据库级别至少需要拥有 `CONTROL DATABASE` 权限。  

问： 配置或审核 IP 防火墙规则的个人或团队是否集中管理多个（可能几百个）数据库的 IP 防火墙规则？
此选择取决于需求和环境。 虽然服务器级别 IP 防火墙规则可能更易于配置，但脚本可以在数据库级别配置规则。 即使使用服务器级别 IP 防火墙规则，也可能需要审核数据库级别 IP 防火墙规则，以确定对数据库拥有 `CONTROL` 权限的用户是否已创建数据库级别 IP 防火墙规则。

问： 能否同时使用服务器级别和数据库级别 IP 防火墙规则？
是的。 一些用户（如管理员）可能需要服务器级别 IP 防火墙规则。 另一些用户（如数据库应用程序用户）可能需要数据库级别 IP 防火墙规则。

## <a name="troubleshooting-the-database-firewall"></a>数据库防火墙故障排除

在对 Microsoft Azure SQL 数据库服务的访问与期望不符时，请考虑以下几点：

- **本地防火墙配置：**

  在计算机可以访问 Azure SQL 数据库之前，可能需要在计算机上创建针对 TCP 端口 1433 的防火墙例外。 如果要在 Azure 云边界内部建立连接，可能需要打开其他端口。 有关详细信息，请参阅 **SQL 数据库：外部与内部**部分（在[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)中）。

- **网络地址转换 (NAT)：**

  由于 NAT 的原因，计算机用来连接到 Azure SQL 数据库的 IP 地址可能不同于计算机 IP 配置设置中显示的 IP 地址。 若要查看计算机用于连接到 Azure 的 IP 地址，请登录门户并导航到托管数据库的服务器上的“**配置**”选项卡。 在“**允许的 IP 地址**”部分下，显示了“**当前客户端 IP 地址**”。 单击“**添加**”即可添加到“**允许的 IP 地址**”，以允许此计算机访问服务器。

- **对允许列表的更改尚未生效：**

  对 Azure SQL 数据库防火墙配置所做的更改可能最多需要 5 分钟的延迟才可生效。

- **登录名未授权或使用了错误的密码：**

  如果某个登录名对 Azure SQL 数据库服务器没有权限或者使用的密码不正确，则与 Azure SQL 数据库服务器的连接会被拒绝。 创建防火墙设置仅向客户端提供尝试连接到服务器的机会；每个客户端必须提供必需的安全凭据。 有关准备登录名的详细信息，请参阅[在 Azure SQL 数据库中管理数据库、登录名和用户](sql-database-manage-logins.md)。

- **动态 IP 地址：**

  如果 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，则可以尝试以下解决方法之一：
  
  - 向 Internet 服务提供商 (ISP) 请求获取分配给访问 Azure SQL 数据库服务器的客户端计算机的 IP 地址范围，然后将此 IP 地址范围添加为 IP 防火墙规则。
  - 改为获取客户端计算机的静态 IP 地址，并将此 IP 地址添加为 IP 防火墙规则。

## <a name="next-steps"></a>后续步骤

- 有关介绍了如何创建服务器级别 IP 防火墙规则的快速入门，请参阅[创建 Azure SQL 数据库](sql-database-single-database-get-started.md)。
- 有关从开放源或第三方应用程序连接到 Azure SQL 数据库的帮助，请参阅 [SQL 数据库的客户端快速入门代码示例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
- 有关可能需要打开的其他端口的信息，请参阅 **SQL 数据库：外部与内部**部分（在[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)中）
- 有关 Azure SQL 数据库安全概述，请参阅[保护数据库](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
