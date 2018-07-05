---
title: 创建和管理弹性池 - Azure SQL 数据库 | Microsoft Docs
description: 创建和管理 Azure SQL 弹性池。
keywords: 多个数据库, 数据库资源, 数据库性能
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/20/2018
ms.author: ninarn
ms.topic: conceptual
ms.reviewer: carlrab
ms.openlocfilehash: 3cdc82d71c05298aa5822b87c22edcc5d8e73385
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311351"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>在 Azure SQL 数据库中创建和管理弹性池

使用弹性池，可以确定弹性池处理其数据库工作负载所需的资源量，以及每个入池数据库的资源量。 

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure 门户：管理弹性池和入池数据库

可在一个位置找到所有池设置：“配置池”边栏选项卡。 若要转到该位置，请在门户中找到所需的弹性池，然后在边栏选项卡顶部，或者在左侧资源菜单中单击“配置池”。

在此处，可以进行以下任意更改组合，并将所有这些更改保存到一个批处理文件中：
1. 更改池的服务层
2. 缩放性能（DTU 或 vCore）和存储
3. 在池中添加或删除数据库
4. 为池中的数据库设置最小（保证）和最大性能限制
5. 检查成本摘要，以查看做出新选择后帐单发生的任何更改

![弹性池配置边栏选项卡](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell：管理弹性池和入池数据库 

若要使用 Azure PowerShell 创建并管理 SQL 数据库弹性池和入池数据库，请使用以下 PowerShell cmdlet。 如果需要安装或升级 PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 若要创建和管理数据库、服务器和防火墙规则，请参阅[使用 PowerShell 创建和管理 Azure SQL 数据库服务器和数据库](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell)。

> [!TIP]
> 有关 PowerShell 示例脚本，请参阅[使用 PowerShell 创建弹性池、在池之间移动数据库以及将数据库移出池](scripts/sql-database-move-database-between-pools-powershell.md)和[使用 PowerShell 监视和缩放 Azure SQL 数据库中的 SQL 弹性池](scripts/sql-database-monitor-and-scale-pool-powershell.md)。
>

| Cmdlet | 说明 |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|在逻辑 SQL 服务器上创建弹性数据库池。|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|获取逻辑 SQL 服务器上的弹性池及其属性值。|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|修改逻辑 SQL 服务器上的弹性数据库池的属性。 例如，使用 **StorageMB** 属性修改弹性池的最大存储。|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|删除逻辑 SQL 服务器上的弹性数据库池。|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|获取逻辑 SQL 服务器上的弹性池的运行状态。|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|在现有池中创建新数据库或将其创建为单一数据库。 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|获取一个或多个数据库。|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|设置数据库的属性，将现有数据库移入、移出弹性池或在其之间移动。|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|删除数据库。|


> [!TIP]
> 使用门户或每次只能创建一个单一数据库的 PowerShell cmdlet 在弹性池中创建多个数据库可能需要一段时间。 若要自动创建到弹性池中，请参阅 [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)。
>

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI：管理弹性池和入池数据库

若要使用 [Azure CLI](/cli/azure) 创建和管理 SQL 数据库弹性池，请使用下面的 [Azure CLI SQL 数据库](/cli/azure/sql/db)命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在浏览器中运行 CLI，或者在 macOS、Linux 或 Windows 上[安装](/cli/azure/install-azure-cli)它。

> [!TIP]
> 有关 Azure CLI 示例脚本，请参阅[使用 CLI 移动 SQL 弹性池中的 Azure SQL 数据库](scripts/sql-database-move-database-between-pools-cli.md)和[使用 Azure CLI 缩放 Azure SQL 数据库中的 SQL 弹性池](scripts/sql-database-scale-pool-cli.md)。
>

| Cmdlet | 说明 |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|创建弹性池。|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|返回服务器中弹性池的列表。|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|返回弹性池中数据库的列表。|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|还包括可用的池 DTU 设置、存储限制和每数据库设置。 为了降低冗长，默认情况下会隐藏附加的存储限制以及每数据库设置。|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|更新弹性池。|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|删除弹性池。|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL：管理入池数据库

若要使用 Transact-SQL 在现有弹性池内创建和移动数据库或者返回有关 SQL 数据库弹性池的信息，请使用以下 T-SQL 命令。 可以使用 Azure 门户、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs) 或可以连接到 Azure SQL 数据库服务器并传递 Transact-SQL 命令的其他任何程序，发出这些命令。 若要创建和管理数据库、服务器和防火墙规则，请参阅[使用 Transact-SQL 创建和管理 Azure SQL 数据库服务器和数据库](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql)。

> [!IMPORTANT]
> 无法使用 Transact-SQL 创建、更新或删除 Azure SQL 数据库弹性池。 可以在弹性池中添加或删除数据库，并且可以使用 DMV 返回有关现有弹性池的信息。
>

| 命令 | 说明 |
| --- | --- |
|[CREATE DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/create-database-azure-sql-database)|在现有池中创建新数据库或将其创建为单一数据库。 必须连接到 master 数据库，才能新建数据库。|
| [ALTER DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/alter-database-azure-sql-database) |将数据库移入、移出弹性池或在其之间移动。|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|删除数据库。|
|[sys.elastic_pool_resource_stats（Azure SQL 数据库）](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|返回逻辑服务器中所有弹性数据库池的资源使用率统计信息。 对于每个弹性数据库池，每 15 秒的报告窗口就会提供一行（每分钟四行）。 这包括池中所有数据库的 CPU、IO、日志和存储消耗以及并发的请求/会话利用率。|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|返回 Azure SQL 数据库或 Azure SQL 数据仓库的版本（服务层）、服务目标（定价层）和弹性池名称（若有）。 如果已登录 Azure SQL 数据库服务器中的 master 数据库，返回所有数据库的相关信息。 对于 Azure SQL 数据仓库，必须连接到 master 数据库。|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API：管理弹性池和入池数据库

若要创建和管理 SQL 数据库弹性池和入池数据库，请使用这些 REST API 请求。

| 命令 | 说明 |
| --- | --- |
|[弹性池 - 创建或更新](/rest/api/sql/elasticpools/createorupdate)|创建新弹性池或更新现有的弹性池。|
|[弹性池 - 删除](/rest/api/sql/elasticpools/delete)|删除弹性池。|
|[弹性池 - 获取](/rest/api/sql/elasticpools/get)|获取弹性池。|
|[弹性池 - 按服务器列出](/rest/api/sql/elasticpools/listbyserver)|返回服务器中弹性池的列表。|
|[弹性池 - 更新](/rest/api/sql/elasticpools/update)|更新现有的弹性池。|
|[推荐的弹性池 - 获取](/rest/api/sql/recommendedelasticpools/get)|获取推荐的弹性池。|
|[推荐的弹性池 - 按服务器列出](/rest/api/sql/recommendedelasticpools/listbyserver)|返回推荐的弹性池。|
|[推荐的弹性池 - 列出指标](/rest/api/sql/recommendedelasticpools/listmetrics)|返回推荐的弹性池指标。|
|[弹性池活动](/rest/api/sql/elasticpoolactivities)|返回弹性池活动。|
|[弹性池数据库活动](/rest/api/sql/elasticpooldatabaseactivities)|返回弹性池内数据库上的活动。|
|[数据库 - 创建或更新](/rest/api/sql/databases/createorupdate)|创建新数据库或更新现有数据库。|
|[数据库 - 获取](/rest/api/sql/databases/get)|获取数据库。|
|[数据库 - 按弹性池获取](/rest/api/sql/databases/getbyelasticpool)|获取弹性池内的数据库。|
|[数据库 - 按推荐的弹性池获取](/rest/api/sql/databases/getbyrecommendedelasticpool)|获取推荐弹性池内的数据库。|
|[数据库 - 按弹性池列出](/rest/api/sql/databases/listbyelasticpool)|返回弹性池中数据库的列表。|
|[数据库 - 按推荐的弹性池列出](/rest/api/sql/databases/listbyrecommendedelasticpool)|返回推荐的弹性池内的数据库列表。|
|[数据库 - 按服务器列出](/rest/api/sql/databases/listbyserver)|返回服务器中的数据库列表。|
|[数据库 - 更新](/rest/api/sql/databases/update)|更新现有的数据库。|

## <a name="next-steps"></a>后续步骤

* 有关视频，请参阅[有关 Azure SQL 数据库弹性功能的 Microsoft 虚拟大学视频课程](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅 [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。
* 有关使用弹性池的 SaaS 教程，请参阅 [Wingtip SaaS 应用程序简介](sql-database-wtp-overview.md)。
