---
title: 创建和管理服务器与单一数据库
description: 了解如何使用 Azure 门户、PowerShell、Azure CLI、Transact-SQL (T-SQL) 和 REST-API 在 Azure SQL 数据库中创建并管理服务器和单一数据库。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 43e28774625db0217dde1227bad160ba87750c8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85254984"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>在 Azure SQL 数据库中创建和管理服务器和单一数据库

可以使用 Azure 门户、PowerShell、Azure CLI、REST API 和 Transact-SQL 在 Azure SQL 数据库中创建和管理服务器和单一数据库。

## <a name="the-azure-portal"></a>Azure 门户

可以提前创建 Azure SQL 数据库的资源组，也可以在创建服务器本身期间创建。

### <a name="create-a-server"></a>创建服务器

若要使用 [Azure 门户](https://portal.azure.com)创建服务器，请从 Azure 市场新建[服务器](logical-servers.md)资源。 或者，可以在部署 Azure SQL 数据库时创建服务器。

  ![创建服务器](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>创建空白或示例数据库

若要使用 [Azure 门户](https://portal.azure.com)创建单个 Azure SQL 数据库，请在 Azure 市场中选择 Azure SQL 数据库资源。 可以提前创建资源组和服务器，也可以在创建单一服务器自身期间创建。 可以创建空白数据库，也可以创建基于 Adventure Works LT 的示例数据库。

  ![创建数据库 - 1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> 有关为数据库选择定价层的信息，请参阅[基于 DTU 的购买模型](service-tiers-dtu.md)和[基于 vCore 的购买模型](service-tiers-vcore.md)。

## <a name="manage-an-existing-server"></a>管理现有服务器

若要管理现有服务器，请使用多种方法转到服务器，如通过特定数据库网页、“SQL 服务器”页或“所有资源”页 。

若要管理现有数据库，请转到“SQL 数据库”页，然后选择要管理的数据库。 下面的屏幕截图展示了如何通过数据库的“概述”页开始为数据库设置服务器级防火墙。

   ![服务器防火墙规则](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> 要配置数据库的性能属性，请参阅[基于 DTU 的购买模型](service-tiers-dtu.md)和[基于 vCore 的购买模型](service-tiers-vcore.md)。
> [!TIP]
> 有关 Azure 门户快速入门，请参阅[在 Azure 门户的 SQL 数据库中创建数据库](single-database-create-quickstart.md)。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

若要使用 Azure PowerShell 创建并管理服务器、单一数据库和共用数据库以及服务器级防火墙，请运行以下 PowerShell cmdlet。 如果需要安装或升级 PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

> [!TIP]
> 有关 PowerShell 示例脚本，请参阅[使用 PowerShell 在 SQL 数据库中创建数据库和配置服务器级防火墙规则](scripts/create-and-configure-database-powershell.md)和[使用 PowerShell 监视和缩放 SQL 数据库中的数据库](scripts/monitor-and-scale-database-powershell.md)。

| Cmdlet | 说明 |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|创建数据库 |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|获取一个或多个数据库|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|设置数据库的属性，或将现有数据库移到弹性池中|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|删除数据库|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|创建资源组|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|创建服务器|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|返回服务器的相关信息|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|修改服务器的属性|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|删除服务器|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|创建服务器级防火墙规则 |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|获取服务器的防火墙规则|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|修改服务器中的防火墙规则|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|从服务器中删除防火墙规则。|
| New-AzSqlServerVirtualNetworkRule | 基于作为虚拟网络服务终结点的子网创建[*虚拟网络规则*](vnet-service-endpoint-rule-overview.md)。 |

## <a name="the-azure-cli"></a>Azure CLI

若要使用 [Azure CLI](/cli/azure) 创建并管理服务器、数据库和防火墙，请运行以下 [Azure CLI](/cli/azure/sql/db) 命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在浏览器中运行 CLI，或者在 macOS、Linux 或 Windows 上[安装](/cli/azure/install-azure-cli)它。 若要创建并管理弹性池，请参阅[弹性池](elastic-pool-overview.md)。

> [!TIP]
> 有关 Azure CLI 快速入门，请参阅[使用 Azure CLI 创建单一 Azure SQL 数据库](az-cli-script-samples-content-guide.md)。 有关 Azure CLI 示例脚本，请参阅[使用 CLI 在 Azure SQL 数据库中创建数据库和配置 SQL 数据库防火墙规则](scripts/create-and-configure-database-cli.md)和[使用 CLI 监视和缩放 Azure SQL 数据库中的数据库](scripts/monitor-and-scale-database-cli.md)。
>

| Cmdlet | 说明 |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |创建数据库|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|列出某台服务器中的所有数据库和数据仓库，或者列出弹性池中的所有数据库|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|列出可用的服务目标和存储限制|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|返回数据库使用情况|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|获取数据库或数据仓库|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|更新数据库|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|删除数据库|
|[az group create](/cli/azure/group#az-group-create)|创建资源组|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|创建服务器|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|列出服务器|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|返回服务器使用情况|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|获取服务器|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|更新服务器|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|删除服务器|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|配置服务器防火墙规则|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|列出服务器上的防火墙规则|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|显示防火墙规则的详细信息|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|更新防火墙规则|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|删除防火墙规则。|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

若要使用 Transact-SQL 创建并管理服务器、数据库和防火墙，请运行以下 T-SQL 命令。 可以使用 Azure 门户、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs) 或可以连接到 SQL 数据库服务器并传递 Transact-SQL 命令的其他任何程序来发出这些命令。 若要管理弹性池，请参阅[弹性池](elastic-pool-overview.md)。

> [!TIP]
> 有关在 Microsoft Windows 上使用 SQL Server Management Studio 的快速入门，请参阅 [Azure SQL 数据库：使用 SQL Server Management Studio 进行连接和数据查询](connect-query-ssms.md)。 有关在 macOS、Linux 或 Windows 上使用 Visual Studio Code 的快速入门，请参阅 [Azure SQL 数据库：使用 Visual Studio Code 进行连接和数据查询](connect-query-vscode.md)。
> [!IMPORTANT]
> 无法使用 Transact-SQL 创建或删除服务器。

| 命令 | 说明 |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|新建单一数据库。 必须连接到 master 数据库才能新建数据库。|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |修改数据库或弹性池。 |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|删除数据库。|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|返回 Azure SQL 数据库或 Azure Synapse Analytics SQL 池的版本（服务层级）、服务目标（定价层）和弹性池名称（若有）。 如果已登录到 SQL 数据库服务器中的 master 数据库，则会返回所有数据库的相关信息。 对于 Azure Synapse Analytics，必须连接到 master 数据库。|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| 返回 Azure SQL 数据库中数据库的 CPU、IO 和内存消耗量。 即使数据库中没有活动，也会每隔 15 秒返回一行数据。|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|返回 Azure SQL 数据库中数据库的 CPU 使用率和存储数据。 在五分钟间隔内收集并聚合数据。|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|包含“SQL 数据库”连接事件的统计信息，提供数据库连接成功和失败状态的概述。 |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|返回成功的“Azure SQL 数据库”连接数、连接失败次数和死锁数。 可以使用此信息跟踪 SQL 数据库的数据库活动并排查其问题。|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|创建或更新服务器的服务器级防火墙设置。 此存储过程仅在服务器级别主体登录名的 master 数据库中可用。 只有在拥有 Azure 级权限的用户创建首个服务器级防火墙规则后，才能使用 Transact-SQL 创建服务器级防火墙规则|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|返回与 Azure SQL 数据库中数据库关联的服务器级防火墙设置的相关信息。|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|从服务器中删除服务器级的防火墙设置。 此存储过程仅在服务器级别主体登录名的 master 数据库中可用。|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|为 Azure SQL 数据库中的数据库创建或更新数据库级防火墙规则。 可以为 master 数据库以及 SQL 数据库上的用户数据库配置数据库防火墙规则。 使用包含的数据库用户时，数据库防火墙规则非常有用。 |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|返回与 Azure SQL 数据库中数据库关联的数据库级防火墙设置的相关信息。 |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|删除数据库中的数据库级防火墙设置。 |

## <a name="rest-api"></a>REST API

若要创建并管理服务器、数据库和防火墙，请使用以下 REST API 请求。

| 命令 | 说明 |
| --- | --- |
|[服务器 - 创建或更新](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|创建或更新新服务器。|
|[Servers - Delete](https://docs.microsoft.com/rest/api/sql/servers/delete)|删除 SQL Server。|
|[Servers - Get](https://docs.microsoft.com/rest/api/sql/servers/get)|获取服务器。|
|[Servers - List](https://docs.microsoft.com/rest/api/sql/servers/list)|返回订阅中的服务器列表。|
|[服务器 - 按资源组列出](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|返回资源组中服务器的列表。|
|[Servers - Update](https://docs.microsoft.com/rest/api/sql/servers/update)|更新现有服务器。|
|[数据库 - 创建或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|创建新数据库或更新现有数据库。|
|[数据库 - 删除](https://docs.microsoft.com/rest/api/sql/databases/delete)|删除数据库。|
|[数据库 - 获取](https://docs.microsoft.com/rest/api/sql/databases/get)|获取数据库。|
|[数据库 - 按弹性池列出](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|返回弹性池中数据库的列表。|
|[数据库 - 按服务器列出](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|返回服务器中的数据库列表。|
|[数据库 - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)|更新现有的数据库。|
|[防火墙规则 - 创建或更新](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|创建或更新防火墙规则。|
|[防火墙规则 - 删除](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|删除防火墙规则。|
|[防火墙规则 - 获取](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|获取防火墙规则。|
|[防火墙规则 - 按服务器列出](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|返回防火墙规则的列表。|

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何将 SQL Server 数据库迁移到 Azure，请参阅[迁移到 Azure SQL 数据库](migrate-to-database-from-sql-server.md)。
- 有关受支持功能的信息，请参阅[功能](features-comparison.md)。
 