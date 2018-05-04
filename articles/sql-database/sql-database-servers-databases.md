---
title: 创建并管理 Azure SQL 服务器和数据库 | Microsoft Docs
description: 了解 Azure SQL 数据库服务器和数据库的概念，以及如何创建和管理服务器和数据库。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/10/2018
ms.author: carlrab
ms.openlocfilehash: 829cedea9752fe41ad24427339d3f13c2f3e371a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="create-and-manage-azure-sql-database-servers-and-databases"></a>创建并管理 Azure SQL 数据库服务器和数据库

SQL 数据库提供了三种类型的数据库：

- 可以使用定义的一组[用于不同工作负载的计算和存储资源](sql-database-service-tiers.md)，在 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)中创建单个数据库。 Azure SQL 数据库与在特定 Azure 区域内创建的 Azure SQL 数据库逻辑服务器相关联。
- 使用定义的一组[用于不同工作负载的计算和存储资源](sql-database-service-tiers.md)（这些计算和存储资源由池中的所有数据库共享）在 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)中创建数据库，作为[数据库池](sql-database-elastic-pool.md)的部分。 Azure SQL 数据库与在特定 Azure 区域内创建的 Azure SQL 数据库逻辑服务器相关联。
- 使用定义的一组用于服务器实例上所有数据库的计算和存储资源，在 [Azure 资源](../azure-resource-manager/resource-group-overview.md)组中创建 [SQL 服务器的实例](sql-database-managed-instance.md)（托管实例）。 一个托管实例同时包含系统和用户数据库。 使用托管实例可将数据库即时转移到完全托管的 PaaS，而无需重新设计应用程序。 托管实例与本地 SQL Server 编程模型高度兼容，支持大多数 SQL Server 功能，并支持随附的工具和服务。  

Microsoft Azure SQL 数据库支持表格格式数据流 (TDS) 协议客户端 7.3 版或更高版本，并仅允许加密的 TCP/IP 连接。

> [!IMPORTANT]
> SQL 数据库托管实例（当前处于公共预览状态）提供单个常规用途服务层。 有关详细信息，请参阅 [SQL 数据库托管实例](sql-database-managed-instance.md)。 本文剩余部分不适用于托管实例。

## <a name="what-is-an-azure-sql-logical-server"></a>什么是 Aure SQL 逻辑服务器？

逻辑服务器用作多个单一或[已共用](sql-database-elastic-pool.md)数据库的中心管理点，包括[登录名](sql-database-manage-logins.md)[防火墙规则](sql-database-firewall-configure.md)[审核规则](sql-database-auditing.md)[威胁检测策略](sql-database-threat-detection.md)和[故障转移组](sql-database-geo-replication-overview.md)。 逻辑服务器可以与其资源组位于不同的区域。 必须先创建逻辑服务器，然后才能创建 Azure SQL 数据库。 服务器上的所有数据库都在逻辑服务器所在的同一区域内创建而成。

逻辑服务器是一种逻辑构造，它不同于在本地环境中可能已熟悉的 SQL Server 实例。 具体而言，SQL 数据库服务不保证数据库相对于其逻辑服务器的位置，并且不公开实例级访问权限或功能。 相反，SQL 数据库托管实例中的服务器与你可能熟悉的本地环境中的 SQL Server 实例相似。

创建逻辑服务器时，提供服务器登录帐户和密码，此凭据有权管理服务器上的 master 数据库及其上创建的所有数据库。 这一初始帐户就是 SQL 登录帐户。 Azure SQL 数据库支持结合使用 SQL 身份验证和 Azure Active Directory 身份验证以进行身份验证。 若要详细了解登录名和身份验证，请参阅[在 Azure SQL 数据库中管理数据库和登录名](sql-database-manage-logins.md)。 不支持 Windows 身份验证。 

> [!TIP]
> 如需有效的资源组和服务器名称，请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。
>

Azure 数据库逻辑服务器：

- 在 Azure 订阅中创建，但可以与其包含的资源一起移到另一个订阅
- 是数据库、弹性池和数据仓库的父资源
- 为数据库、弹性池和数据仓库提供命名空间
- 是具有强生存期语义的逻辑容器。也就是说，删除服务器也会随之删除包含的数据库、弹性池和数据仓库
- 参与 [Azure 基于角色的访问控制 (RBAC)](/azure/role-based-access-control/overview)。也就是说，服务器中的数据库、弹性池和数据库从服务器继承访问权限
- 是位置靠前的数据库、弹性池和数据仓库标识元素，用于管理 Azure 资源（请参阅“数据库和池的 URL 方案”）
- 并置区域中的资源
- 为数据库访问提供连接终结点 (<serverName>.database.windows.net)
- 连接到 master 数据库通过 DMV 提供对与包含资源相关的元数据的访问 
- 提供应用于数据库的管理策略的作用域，即登录名、防火墙、审核、威胁检测等。 
- 受父订阅中的配额限制（默认情况下，每个订阅二十台服务器，[请单击此处了解订阅限制](../azure-subscription-service-limits.md)）
- 提供所含资源的数据库配额和 DTU 或 vCore 配额范围（例如，45,000 个 DTU）
- 是在包含资源上启用的功能的版本控制作用域 
- 服务器级主体登录名可以管理服务器上的所有数据库
- 可以包含与本地 SQL Server 实例中的登录名类似的登录名，这些登录名有权访问服务器上的一个或多个数据库，并可以向这些登录名授予有限的管理权限。 有关详细信息，请参阅[登录名](sql-database-manage-logins.md)。
- 用于所有在逻辑服务器上创建的用户数据库的默认排序规则是 `SQL_LATIN1_GENERAL_CP1_CI_AS`，其中 `LATIN1_GENERAL` 为英语（美国），`CP1` 为代码页 1252，`CI` 不区分大小写，`AS` 区分重音符。

## <a name="azure-sql-databases-protected-by-sql-database-firewall"></a>Azure SQL 数据库受 SQL 数据库防火墙保护

为了帮助保数据，[SQL 数据库防火墙](sql-database-firewall-configure.md)会阻止通过以下方式访问数据库服务器或其所有数据库：不是直接通过 Azure 订阅连接与服务器相连。 若要启用其他连接方案，必须[创建一个或多个防火墙规则](sql-database-firewall-configure.md#creating-and-managing-firewall-rules)。 若要创建并管理弹性池，请参阅[弹性池](sql-database-elastic-pool.md)。

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>使用 Azure 门户管理 Azure SQL 服务器、数据库和防火墙

可以提前创建 Azure SQL 数据库的资源组，也可以在创建服务器本身期间创建。 

### <a name="create-a-blank-sql-server-logical-server"></a>创建空白 SQL 服务器（逻辑服务器）

若要使用 [Azure 门户](https://portal.azure.com)创建 Azure SQL 数据库服务器（不含数据库），请转到一个空白 SQL（逻辑）服务器表单。  

### <a name="create-a-blank-or-sample-sql-database"></a>创建空白或示例 SQL 数据库

若要使用 [Azure 门户](https://portal.azure.com)创建 Azure SQL 数据库，请转到空白 SQL 数据库表单，并输入相应信息。 可以提前创建 Azure SQL 数据库的资源组和逻辑服务器，也可以在创建数据库本身期间创建。 可以创建空白数据库，也可以创建基于 Adventure Works LT 的示例数据库。 

  ![创建数据库 - 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> 有关如何选择数据库定价层的信息，请参阅[服务层](sql-database-service-tiers.md)。

要创建托管实例，请参阅[创建托管实例](sql-database-managed-instance-create-tutorial-portal.md)

### <a name="manage-an-existing-sql-server"></a>管理现有 SQL 服务器

若要管理现有服务器，请使用多种方法转到服务器，如通过特定 SQL 数据库网页、“SQL 服务器”页或“所有资源”页。 

若要管理现有数据库，请转到“SQL 数据库”页，再单击要管理的数据库。 下面的屏幕截图展示了如何通过数据库的“概述”页开始为数据库设置服务器级防火墙。 

   ![服务器防火墙规则](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> 若要配置数据库的性能属性，请参阅[服务层](sql-database-service-tiers.md)。
>

> [!TIP]
> 有关 Azure 门户快速入门，请参阅[在 Azure 门户中创建 Azure SQL 数据库](sql-database-get-started-portal.md)。
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>使用 PowerShell 管理 Azure SQL 服务器、数据库和防火墙

若要使用 Azure PowerShell 创建并管理 Azure SQL 服务器、数据库和防火墙，请运行以下 PowerShell cmdlet。 如果需要安装或升级 PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 若要创建并管理弹性池，请参阅[弹性池](sql-database-elastic-pool.md)。

| Cmdlet | 说明 |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|创建数据库 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|获取一个或多个数据库|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|设置数据库的属性，或将现有数据库移到弹性池中|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|删除数据库|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|创建资源组
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|创建服务器|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|返回服务器的相关信息|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|修改服务器的属性|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|删除服务器|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|创建服务器级防火墙规则 |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|获取服务器的防火墙规则|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|修改服务器中的防火墙规则|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|从服务器中删除防火墙规则。|
| New-AzureRmSqlServerVirtualNetworkRule | 基于作为虚拟网络服务终结点的子网创建[*虚拟网络规则*](sql-database-vnet-service-endpoint-rule-overview.md)。 |

> [!TIP]
> 有关 PowerShell 快速入门，请参阅[使用 PowerShell 创建单一 Azure SQL 数据库](sql-database-get-started-portal.md)。 有关 PowerShell 示例脚本，请参阅[使用 PowerShell 创建单个 Azure SQL 数据库和配置防火墙规则](scripts/sql-database-create-and-configure-database-powershell.md)和[使用 PowerShell 监视和缩放单一 SQL 数据库](scripts/sql-database-monitor-and-scale-database-powershell.md)。
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure SQL 服务器、数据库和防火墙

若要使用 [Azure CLI](/cli/azure) 创建并管理 Azure SQL 服务器、数据库和防火墙，请运行 [Azure CLI SQL 数据库](/cli/azure/sql/db)命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在浏览器中运行 CLI，或者在 macOS、Linux 或 Windows 上[安装](/cli/azure/install-azure-cli)它。 若要创建并管理弹性池，请参阅[弹性池](sql-database-elastic-pool.md)。

| Cmdlet | 说明 |
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

> [!TIP]
> 有关 Azure CLI 快速入门，请参阅[使用 Azure CLI 创建单一 Azure SQL 数据库](sql-database-get-started-cli.md)。 有关 Azure CLI 示例脚本，请参阅[使用 CLI 创建单个 Azure SQL 数据库和配置防火墙规则](scripts/sql-database-create-and-configure-database-cli.md)和[使用 CLI 监视和缩放单一 SQL 数据库](scripts/sql-database-monitor-and-scale-database-cli.md)。
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>使用 Transact-SQL 管理 Azure SQL 服务器、数据库和防火墙

若要使用 Transact-SQL 创建并管理 Azure SQL 服务器、数据库和防火墙，请运行以下 T-SQL 命令。 可以使用 Azure 门户、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs) 或可以连接到 Azure SQL 数据库服务器并传递 Transact-SQL 命令的其他任何程序，发出这些命令。 若要管理弹性池，请参阅[弹性池](sql-database-elastic-pool.md)。

> [!IMPORTANT]
> 无法使用 Transact-SQL 创建或删除服务器。
>

| 命令 | 说明 |
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


> [!TIP]
> 有关在 Microsoft Windows 上使用 SQL Server Management Studio 的快速入门，请参阅 [Azure SQL 数据库：使用 SQL Server Management Studio 进行连接和数据查询](sql-database-connect-query-ssms.md)。 有关在 macOS、Linux 或 Windows 上使用 Visual Studio Code 的快速入门，请参阅 [Azure SQL 数据库：使用 Visual Studio Code 进行连接和数据查询](sql-database-connect-query-vscode.md)。

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>使用 REST API 管理 Azure SQL 服务器、数据库和防火墙

若要创建并管理 Azure SQL 服务器、数据库和防火墙，请使用以下 REST API 请求。

| 命令 | 说明 |
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
