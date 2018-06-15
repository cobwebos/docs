---
title: Azure SQL 数据库防火墙规则 | Microsoft Docs
description: 了解如何配置使用服务器级和数据库级防火墙规则的 SQL 数据库防火墙以管理访问权限。
keywords: 数据库防火墙
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 1848fea8a21afc473873cb8c4a5ba87b09da02e2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646807"
---
# <a name="azure-sql-database-server-level-and-database-level-firewall-rules"></a>Azure SQL 数据库服务器级和数据库级防火墙规则 

Microsoft Azure SQL 数据库为 Azure 和其他基于 Internet 的应用程序提供关系数据库服务。 为了保护数据，在指定哪些计算机具有访问权限之前，防火墙将禁止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。

#### <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>将虚拟网络规则作为 IP 规则的替代

除了 IP 规则外，防火墙还管理*虚拟网络规则*。 虚拟网络规则基于虚拟网络服务终结点。 在某些情况下，虚拟网络规则可能比 IP 规则更好。 若要了解详细信息，请参阅[虚拟网络服务终结点和 Azure SQL 数据库规则](sql-database-vnet-service-endpoint-rule-overview.md)。

## <a name="overview"></a>概述

最初，防火墙会阻止对 Azure SQL Server 的所有 Transact-SQL 访问。 若要开始使用 Azure SQL Server，必须指定允许访问 Azure SQL Server 的一个或多个服务器级防火墙规则。 使用防火墙规则可以指定允许的 Internet 上的 IP 地址范围，以及 Azure 应用程序是否可以尝试连接到 Azure SQL Server。

若要有选择地授予对 Azure SQL Server 中的一个数据库的访问权限，必须针对所需的数据库创建数据库级规则。 针对超出服务器级防火墙规则中指定的 IP 地址范围的数据库防火墙规则指定一个 IP 地址范围，并确保客户端的 IP 地址位于数据库级规则中指定的范围内。

来自 Internet 和 Azure 的连接尝试必须首先通过防火墙，才能访问 Azure SQL Server 或 SQL 数据库，如下图中所示：

   ![描述防火墙配置的示意图。][1]

* **服务器级防火墙规则**：这些规则允许客户端访问整个 Azure SQL Server，即同一逻辑服务器内的所有数据库。 这些规则存储在 **master** 数据库中。 可以通过使用门户或 Transact-SQL 语句配置服务器级防火墙规则。 若要使用 Azure 门户或 PowerShell 创建服务器级别的防火墙规则，用户必须是订阅所有者或订阅参与者。 若要使用 Transact-SQL 创建服务器级别的防火墙规则，用户必须以服务器级别的主登录名或 Azure Active Directory 管理员的身份连接到 SQL 数据库实例（这意味着，必须首先由具有 Azure 级别权限的用户创建服务器级别的防火墙规则）。
* **数据库级防火墙规则**：这些规则允许客户端访问同一逻辑服务器内的某个（安全）数据库。 可以为每个数据库创建这些规则（包括 master 数据库），它们将存储在单独的数据库中。 用于 master 数据库和用户数据库的数据库级防火墙规则只能通过使用 Transact-SQL 语句进行创建和托管，而且只能在配置了第一个服务器级别的防火墙后才能执行上述操作。 如果在数据库级防火墙规则中指定的 IP 地址范围超出了在服务器级防火墙规则中指定的范围，则客户端要访问数据库，其 IP 地址必须处于数据库级范围内。 对于每个数据库，最多可以有 128 个数据库级防火墙规则。 有关配置数据库级防火墙规则的详细信息，请参阅本文后面部分的示例以及 [sp_set_database_firewall_rule（Azure SQL 数据库）](https://msdn.microsoft.com/library/dn270010.aspx)。

**建议**：Microsoft 建议尽量使用数据库级防火墙规则，以增强安全性并提高数据库的可移植性。 当多个数据库具有相同的访问要求，但你不想花时间分别配置每个数据库时，可使用管理员的服务器级防火墙规则。

> [!Important]
> Microsoft Azure SQL 数据库支持最多 128 个防火墙规则。
>

> [!Note]
> 有关业务连续性上下文中的可移植数据库的信息，请参阅[灾难恢复的身份验证要求](sql-database-geo-replication-security-config.md)。
>

### <a name="connecting-from-the-internet"></a>从 Internet 连接

在计算机尝试从 Internet 连接到数据库服务器时，防火墙首先根据数据库级防火墙规则（针对该连接所请求的数据库）检查该请求的发起 IP 地址：

* 如果该请求的 IP 地址位于数据库级防火墙规则中指定的某个范围内，则将连接权限授予包含该规则的 SQL 数据库。
* 如果该请求的 IP 地址不位于数据库级防火墙规则中指定的某个范围内，则检查服务器级防火墙规则。 如果该请求的 IP 地址位于服务器级防火墙规则中指定的某个范围内，则授予连接权限。 服务器级防火墙规则适用于 Azure SQL Server 上的所有 SQL 数据库。  
* 如果该请求的 IP 地址不位于任何数据库级或服务器级防火墙规则中指定的范围内，则连接请求失败。

> [!NOTE]
> 要从本地计算机访问 Azure SQL 数据库，请确保网络和本地计算机上的防火墙允许在 TCP 端口 1433 上的传出通信。
> 

### <a name="connecting-from-azure"></a>从 Azure 连接
若要允许来自 Azure 的应用程序连接到 Azure SQL Server，则必须启用 Azure 连接。 在应用程序尝试从 Azure 连接到数据库服务器时，防火墙会验证是否允许 Azure 连接。 如果防火墙设置的开始地址和结束地址都等于 0.0.0.0，则表示允许这些连接。 如果不允许该连接尝试，则该请求将不会访问 Azure SQL 数据库服务器。

> [!IMPORTANT]
> 该选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问权限限制为仅已授权用户使用。
> 

## <a name="creating-and-managing-firewall-rules"></a>创建和管理防火墙规则
第一个服务器级防火墙设置可以使用 [Azure 门户](https://portal.azure.com/)进行创建，也可以使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql)、[Azure CLI](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) 或 [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules) 通过编程方式创建。 后续的服务器级防火墙规则可以使用这些方法或通过 Transact-SQL 创建和管理。 

> [!IMPORTANT]
> 只能使用 Transact-SQL 创建和管理数据库级防火墙规则。 
>

为了提高性能，会暂时在数据库级别缓存服务器级防火墙规则。 若要刷新高速缓存，请参阅 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)。 

> [!TIP]
> 可以使用[SQL 数据库审核](sql-database-auditing.md)来审核服务器级别和数据库级别防火墙的更改。
>

## <a name="manage-firewall-rules-using-the-azure-portal"></a>使用 Azure 门户管理防火墙规则

若要在 Azure 门户中设置服务器级防火墙规则，可以转到 Azure SQL 数据库的“概述”页面或 Azure 数据库逻辑服务器的“概述”页面。

> [!TIP]
> 有关教程，请参阅[使用 Azure 门户创建 DB](sql-database-get-started-portal.md)。
>

**从数据库概述页**

1. 若要从数据库概述页设置服务器级防火墙规则，请单击工具栏上的“设置服务器防火墙”，如下图所示：SQL 数据库服务器的“防火墙设置”页面随即打开。

      ![服务器防火墙规则](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. 单击工具栏上的“添加客户端 IP”以添加当前使用的计算机的 IP 地址，并单击“保存”。 此时会针对当前的 IP 地址创建服务器级防火墙规则。

      ![设置服务器防火墙规则](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

**从服务器概述页面**

此时会打开服务器的概述页，其中显示了完全限定的服务器名称（例如 **mynewserver20170403.database.windows.net**），并提供了其他配置的选项。

1. 若要从服务器概述页设置服务器级规则，请在“设置”下方单击左侧菜单中的“防火墙”： 

2. 单击工具栏上的“添加客户端 IP”以添加当前使用的计算机的 IP 地址，并单击“保存”。 此时会针对当前的 IP 地址创建服务器级防火墙规则。

## <a name="manage-firewall-rules-using-transact-sql"></a>使用 Transact-SQL 管理防火墙规则
| 目录视图或存储过程 | 级别 | 说明 |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |服务器 |显示当前的服务器级防火墙规则 |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |服务器 |创建或更新服务器级防火墙规则 |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |服务器 |删除服务器级防火墙规则 |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |数据库 |显示当前的数据库级防火墙规则 |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |数据库 |创建或更新数据库级防火墙规则 |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |数据库 |删除数据库级防火墙规则 |


以下示例会检查现有规则、在服务器 Contoso 上启用一系列 IP 地址，并删除防火墙规则：
   
```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```
  
其次，添加防火墙规则。
   
```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

若要删除服务器级别防火墙规则，请执行 sp_delete_firewall_rule 存储过程。 以下示例将删除名为 ContosoFirewallRule 的规则：
   
```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```   

## <a name="manage-firewall-rules-using-azure-powershell"></a>使用 Azure PowerShell 管理防火墙规则
| Cmdlet | 级别 | 说明 |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |服务器 |返回当前的服务器级防火墙规则 |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |服务器 |新建服务器级防火墙规则 |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |服务器 |更新现有服务器级防火墙规则的属性 |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |服务器 |删除服务器级防火墙规则 |


以下示例使用 PowerShell 设置服务器级防火墙规则：

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> 对于快速入门上下文中的 PowerShell 示例，请参阅[创建 DB - PowerShell](sql-database-get-started-powershell.md) 和[使用 PowerShell 创建单一数据库并配置防火墙规则](scripts/sql-database-create-and-configure-database-powershell.md)
>

## <a name="manage-firewall-rules-using-azure-cli"></a>使用 Azure CLI 管理防火墙规则
| Cmdlet | 级别 | 说明 |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|服务器|配置服务器防火墙规则|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|服务器|列出服务器上的防火墙规则|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|服务器|显示防火墙规则的详细信息|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|服务器|更新防火墙规则|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|服务器|删除防火墙规则|

以下示例使用 Azure CLI 设置服务器级防火墙规则： 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> 对于快速入门上下文中的 Azure CLI 示例，请参阅[创建 DDB - Azure CLI](sql-database-get-started-cli.md) 和[使用 Azure CLI 创建单一数据库并配置防火墙规则](scripts/sql-database-create-and-configure-database-cli.md)
>

## <a name="manage-firewall-rules-using-rest-api"></a>使用 REST API 管理防火墙规则
| API | 级别 | 说明 |
| --- | --- | --- |
| [列出防火墙规则](https://docs.microsoft.com/rest/api/sql/FirewallRules/ListByServer) |服务器 |显示当前的服务器级防火墙规则 |
| [创建或更新防火墙规则](https://docs.microsoft.com/rest/api/sql/FirewallRules/CreateOrUpdate) |服务器 |创建或更新服务器级防火墙规则 |
| [删除防火墙规则](https://docs.microsoft.com/rest/api/sql/FirewallRules/Delete) |服务器 |删除服务器级防火墙规则 |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>服务器级防火墙规则与数据库级防火墙规则
问： 是否应将一个数据库的用户与另一个数据库完全隔离？   
  如果是，则使用数据库级防火墙规则授予访问权限。 这样可以避免使用服务器级防火墙规则（此规则允许通过防火墙访问所有数据库，从而降低防御程度）。   
 
问： IP 地址用户是否需要访问所有数据库？   
  使用服务器级防火墙规则可减少必须配置防火墙规则的次数。   

问： 配置防火墙规则的个人或团队是否仅通过 Azure 门户、PowerShell 或 REST API 具有访问权限？   
  必须使用服务器级防火墙规则。 只能使用 Transact-SQL 配置数据库级防火墙规则。  

问： 是否禁止配置防火墙规则的个人或团队拥有数据库级别的高级权限？   
  使用服务器级防火墙规则。 如果使用 Transact-SQL 配置数据库级防火墙规则，至少需要数据库级别的 `CONTROL DATABASE` 权限。  

问： 配置或审核防火墙规则的个人或团队是否集中管理多个（可能几百个）数据库的防火墙规则？   
  此选择取决于需求和环境。 服务器级防火墙规则可能较容易配置，但脚本可以在数据库级别配置规则。 即使使用服务器级防火墙规则，也可能需要审核数据库防火墙规则，以查看对数据库具有 `CONTROL` 权限的用户是否已创建数据库级防火墙规则。   

问： 是否可以同时使用服务器级和数据库级防火墙规则？   
  是的。 某些用户（如管理员）可能需要服务器级防火墙规则。 其他用户（如数据库应用程序的用户）可能需要数据库级防火墙规则。   

## <a name="troubleshooting-the-database-firewall"></a>数据库防火墙故障排除
在对 Microsoft Azure SQL 数据库服务的访问与期望不符时，请考虑以下几点：

* **本地防火墙配置**：在计算机可以访问 Azure SQL 数据库之前，可能需要在计算机上创建针对 TCP 端口 1433 的防火墙例外。 如果要在 Azure 云边界内部建立连接，可能需要打开其他端口。 有关详细信息，请参阅[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)中的 **SQL 数据库：外部与内部**部分。
* **网络地址转换 (NAT)：** 由于 NAT 的原因，计算机用来连接到 Azure SQL 数据库的 IP 地址可能不同于计算机 IP 配置设置中显示的 IP 地址。 若要查看计算机用于连接到 Azure 的 IP 地址，请登录门户并导航到托管数据库的服务器上的“**配置**”选项卡。 在“**允许的 IP 地址**”部分下，显示了“**当前客户端 IP 地址**”。 单击“**添加**”即可添加到“**允许的 IP 地址**”，以允许此计算机访问服务器。
* **对允许列表的更改尚未生效：** 对 Azure SQL 数据库防火墙配置所做的更改可能最多需要 5 分钟的延迟才可生效。
* **登录名未授权或使用了错误的密码：** 如果某个登录名对 Azure SQL 数据库服务器没有权限或者使用的密码不正确，则与 Azure SQL 数据库服务器的连接会被拒绝。 创建防火墙设置仅向客户端提供尝试连接到服务器的机会；每个客户端必须提供必需的安全凭据。 有关准备登录名的详细信息，请参阅[在 Azure SQL 数据库中管理数据库、登录名和用户](sql-database-manage-logins.md)。
* **动态 IP 地址：** 如果 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，则可以尝试以下解决方法之一：
  
  * 向 Internet 服务提供商 (ISP) 询问分配给客户端计算机的将用来访问 Azure SQL 数据库服务器的 IP 地址范围，然后将该 IP 地址范围作为防火墙规则添加。
  * 改为获取客户端计算机的静态 IP 地址，并将该 IP 地址作为防火墙规则添加。

## <a name="next-steps"></a>后续步骤

- 有关创建数据库级和服务器级防火墙规则的快速入门，请参阅[创建 Azure SQL 数据库](sql-database-get-started-portal.md)。
- 有关从开放源或第三方应用程序连接到 Azure SQL 数据库的帮助，请参阅 [SQL 数据库的客户端快速入门代码示例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
- 有关可能需要打开的其他端口的信息，请参阅[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)中的 **SQL 数据库：外部与内部**部分
- 有关 Azure SQL 数据库安全概述，请参阅[保护数据库](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
