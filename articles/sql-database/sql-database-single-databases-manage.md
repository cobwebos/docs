---
title: 创建、管理 Azure SQL 服务器和单一数据库 | Microsoft Docs
description: 了解如何创建和管理逻辑服务器和单一数据库。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: single-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: a94c3a4c4b8ffb22b1d75ca064bd3e48a2e50141
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005672"
---
# <a name="create-and-manage-logical-servers-and-single-databases-in-azure-sql-database"></a>在 Azure SQL 数据库中创建和管理逻辑服务器和单一数据库 

可以使用 Azure 门户、PowerShell、Azure CLI、REST API 和 Transact-SQL 创建和管理 Azure SQL 数据库逻辑服务器和单一数据库。

## <a name="azure-portal-manage-logical-servers-and-databases"></a>Azure 门户：管理逻辑服务器和数据库

可以提前创建 Azure SQL 数据库的资源组，也可以在创建服务器本身期间创建。 转到新 SQL 服务器表单的方法有多种，可以通过新建 SQL 服务器，也可以在新建数据库期间。 

### <a name="create-a-blank-sql-server-logical-server"></a>创建空白 SQL 服务器（逻辑服务器）

若要使用 [Azure 门户](https://portal.azure.com)创建 Azure SQL 数据库服务器（不含数据库），请转到空白 SQL 服务器（逻辑服务器）表单。  

### <a name="create-a-blank-or-sample-sql-database"></a>创建空白或示例 SQL 数据库

若要使用 [Azure 门户](https://portal.azure.com)创建 Azure SQL 数据库，请转到空白 SQL 数据库表单，并输入相应信息。 可以提前创建 Azure SQL 数据库的资源组和逻辑服务器，也可以在创建数据库本身期间创建。 可以创建空白数据库，也可以创建基于 Adventure Works LT 的示例数据库。 

  ![创建数据库 - 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> 有关为数据库选择定价层的信息，请参阅[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。

要创建托管实例，请参阅[创建托管实例](sql-database-managed-instance-create-tutorial-portal.md)

### <a name="manage-an-existing-sql-server"></a>管理现有 SQL 服务器

若要管理现有服务器，请使用多种方法转到服务器，如通过特定 SQL 数据库网页、“SQL 服务器”页或“所有资源”页。 

若要管理现有数据库，请转到“SQL 数据库”页，再单击要管理的数据库。 下面的屏幕截图展示了如何通过数据库的“概述”页开始为数据库设置服务器级防火墙。 

   ![服务器防火墙规则](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> 要配置数据库的性能属性，请参阅[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。
>

> [!TIP]
> 有关 Azure 门户快速入门，请参阅[在 Azure 门户中创建 Azure SQL 数据库](sql-database-get-started-portal.md)。

## <a name="powershell-manage-logical-servers-and-databases"></a>PowerShell：管理逻辑服务器和数据库

若要使用 Azure PowerShell 创建并管理 Azure SQL 服务器、数据库和防火墙，请运行以下 PowerShell cmdlet。 如果需要安装或升级 PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 

> [!TIP]
> 有关 PowerShell 快速入门，请参阅[使用 PowerShell 创建单一 Azure SQL 数据库](sql-database-get-started-portal.md)。 有关 PowerShell 示例脚本，请参阅[使用 PowerShell 创建单个 Azure SQL 数据库和配置防火墙规则](scripts/sql-database-create-and-configure-database-powershell.md)和[使用 PowerShell 监视和缩放单一 SQL 数据库](scripts/sql-database-monitor-and-scale-database-powershell.md)。
>

| Cmdlet | Description |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|创建数据库 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|获取一个或多个数据库|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|设置数据库的属性，或将现有数据库移到弹性池中|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|删除数据库|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|创建资源组|
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|创建服务器|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|返回服务器的相关信息|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|修改服务器的属性|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|删除服务器|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|创建服务器级防火墙规则 |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|获取服务器的防火墙规则|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|修改服务器中的防火墙规则|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|从服务器中删除防火墙规则。|
| New-AzureRmSqlServerVirtualNetworkRule | 基于作为虚拟网络服务终结点的子网创建[*虚拟网络规则*](sql-database-vnet-service-endpoint-rule-overview.md)。 |

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Azure CLI：管理逻辑服务器和数据库

若要使用 [Azure CLI](/cli/azure) 创建并管理 Azure SQL 服务器、数据库和防火墙，请使用以下 [Azure CLI SQL 数据库](/cli/azure/sql/db)命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在浏览器中运行 CLI，或者在 macOS、Linux 或 Windows 上[安装](/cli/azure/install-azure-cli)它。 若要创建并管理弹性池，请参阅[弹性池](sql-database-elastic-pool.md)。

> [!TIP]
> 有关 Azure CLI 快速入门，请参阅[使用 Azure CLI 创建单一 Azure SQL 数据库](sql-database-get-started-cli.md)。 有关 Azure CLI 示例脚本，请参阅[使用 CLI 创建单个 Azure SQL 数据库和配置防火墙规则](scripts/sql-database-create-and-configure-database-cli.md)和[使用 CLI 监视和缩放单一 SQL 数据库](scripts/sql-database-monitor-and-scale-database-cli.md)。
>

| Cmdlet | Description |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |创建数据库|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|列出某台服务器中的所有数据库和数据仓库，或者列出弹性池中的所有数据库|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|列出可用的服务目标和存储上限|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|返回数据库使用情况|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|获取数据库或数据仓库|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|更新数据库|
|[az sql db delete](/cli/azure/sql/db#az_sql_db_delete)|删除数据库|
|[az group create](/cli/azure/group#az_group_create)|创建资源组|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|创建服务器|
|[az sql server list](/cli/azure/sql/server#az_sql_server_list)|列出服务器|
|[az sql server list-usages](/cli/azure/sql/server#az_sql_server_list_usages)|返回服务器使用情况|
|[az sql server show](/cli/azure/sql/server#az_sql_server_show)|获取服务器|
|[az sql server update](/cli/azure/sql/server#az_sql_server_update)|更新服务器|
|[az sql server delete](/cli/azure/sql/server#az_sql_server_delete)|删除服务器|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|配置服务器防火墙规则|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|列出服务器上的防火墙规则|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|显示防火墙规则的详细信息|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|更新防火墙规则|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|删除防火墙规则|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Transact-SQL：管理逻辑服务器和数据库

若要使用 Transact-SQL 创建并管理 Azure SQL 服务器、数据库和防火墙，请运行以下 T-SQL 命令。 可以使用 Azure 门户、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs) 或可以连接到 Azure SQL 数据库服务器并传递 Transact-SQL 命令的其他任何程序，发出这些命令。 若要管理弹性池，请参阅[弹性池](sql-database-elastic-pool.md)。


> [!TIP]
> 有关在 Microsoft Windows 上使用 SQL Server Management Studio 的快速入门，请参阅 [Azure SQL 数据库：使用 SQL Server Management Studio 进行连接和数据查询](sql-database-connect-query-ssms.md)。 有关在 macOS、Linux 或 Windows 上使用 Visual Studio Code 的快速入门，请参阅 [Azure SQL 数据库：使用 Visual Studio Code 进行连接和数据查询](sql-database-connect-query-vscode.md)。

> [!IMPORTANT]
> 无法使用 Transact-SQL 创建或删除服务器。
>

| 命令 | Description |
| --- | --- |
|[CREATE DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/create-database-azure-sql-database)|新建数据库。 必须连接到 master 数据库，才能新建数据库。|
| [ALTER DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/alter-database-azure-sql-database) |修改 Azure SQL 数据库。 |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|修改 Azure SQL 数据仓库。|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|删除数据库。|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|返回 Azure SQL 数据库或 Azure SQL 数据仓库的版本（服务层）、服务目标（定价层）和弹性池名称（若有）。 如果已登录 Azure SQL 数据库服务器中的 master 数据库，返回所有数据库的相关信息。 对于 Azure SQL 数据仓库，必须连接到 master 数据库。|
|[sys.dm_db_resource_stats（Azure SQL 数据库）](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| 返回 Azure SQL 数据库中数据库的 CPU、IO 和内存消耗量。 即使数据库中没有活动，也会每 15 秒存在一行。|
|[sys.resource_stats（Azure SQL 数据库）](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|返回 Azure SQL 数据库的 CPU 使用率和存储数据。 在五分钟的间隔内收集和聚合数据。|
|[sys.database_connection_stats（Azure SQL 数据库）](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|包含 SQL 数据库的数据库连接事件的统计信息，并提供数据库连接成功和失败的概述。 |
|[sys.event_log（Azure SQL 数据库）](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|返回成功的 Azure SQL 数据库的数据库连接数、连接失败数和死锁数。 可以使用此信息跟踪或排查 SQL 数据库的数据库活动。|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|创建或更新 SQL 数据库服务器的服务器级防火墙设置。 此存储过程仅在服务器级别主体登录名的 master 数据库中可用。 只有在拥有 Azure 级权限的用户创建首个服务器级防火墙规则后，才能使用 Transact-SQL 创建服务器级防火墙规则|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|返回与 Microsoft Azure SQL 数据库关联的服务器级防火墙设置的相关信息。|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|从 SQL 数据库服务器中删除服务器级防火墙设置。 此存储过程仅在服务器级别主体登录名的 master 数据库中可用。|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|创建或更新 Azure SQL 数据库或 SQL 数据仓库的数据库级防火墙规则。 可以为 master 数据库以及 SQL 数据库上的用户数据库配置数据库防火墙规则。 使用包含的数据库用户时，数据库防火墙规则非常有用。 |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|返回与 Microsoft Azure SQL 数据库关联的数据库级防火墙设置的相关信息。 |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|从 Azure SQL 数据库或 SQL 数据仓库中删除数据库级防火墙规则设置。 |



## <a name="rest-api-manage-logical-servers-and-databases"></a>REST API：管理逻辑服务器和数据库

若要创建并管理 Azure SQL 服务器、数据库和防火墙，请使用以下 REST API 请求。

| 命令 | Description |
| --- | --- |
|[Servers - Create Or Update](/rest/api/sql/servers/createorupdate)|创建或更新新服务器。|
|[Servers - Delete](/rest/api/sql/servers/delete)|删除 SQL Server。|
|[Servers - Get](/rest/api/sql/servers/get)|获取服务器。|
|[Servers - List](/rest/api/sql/servers/list)|返回服务器的列表。|
|[Servers - List By Resource Group](/rest/api/sql/servers/listbyresourcegroup)|返回资源组中服务器的列表。|
|[Servers - Update](/rest/api/sql/servers/update)|更新现有服务器。|
|[数据库 - 创建或更新](/rest/api/sql/databases/createorupdate)|创建新数据库或更新现有数据库。|
|[数据库 - 获取](/rest/api/sql/databases/get)|获取数据库。|
|[数据库 - 按弹性池获取](/rest/api/sql/databases/getbyelasticpool)|获取弹性池内的数据库。|
|[数据库 - 按推荐的弹性池获取](/rest/api/sql/databases/getbyrecommendedelasticpool)|获取推荐弹性池内的数据库。|
|[数据库 - 按弹性池列出](/rest/api/sql/databases/listbyelasticpool)|返回弹性池中数据库的列表。|
|[数据库 - 按推荐的弹性池列出](/rest/api/sql/databases/listbyrecommendedelasticpool)|返回推荐的弹性池内的数据库列表。|
|[数据库 - 按服务器列出](/rest/api/sql/databases/listbyserver)|返回服务器中的数据库列表。|
|[数据库 - 更新](/rest/api/sql/databases/update)|更新现有的数据库。|
|[Firewall Rules - Create Or Update](/rest/api/sql/firewallrules/createorupdate)|创建或更新防火墙规则。|
|[Firewall Rules - Delete](/rest/api/sql/firewallrules/delete)|删除防火墙规则。|
|[Firewall Rules - Get](/rest/api/sql/firewallrules/get)|获取防火墙规则。|
|[Firewall Rules - List By Server](/rest/api/sql/firewallrules/listbyserver)|返回防火墙规则的列表。|

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何将 SQL Server 数据库迁移到 Azure，请参阅[迁移到 Azure SQL 数据库](sql-database-cloud-migrate.md)。
- 有关支持的功能的信息，请参阅[功能](sql-database-features.md)。
