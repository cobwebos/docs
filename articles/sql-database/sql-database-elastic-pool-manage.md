---
title: 管理弹性池-Azure SQL 数据库 |Microsoft Docs
description: 创建和管理 Azure SQL 弹性池。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: debf91f04cff3cb9705ebc5915e2e665679230a9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267576"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>管理 Azure SQL 数据库中的弹性池

使用弹性池，可以确定弹性池处理其数据库工作负载所需的资源量，以及每个入池数据库的资源量。

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure 门户：管理弹性池和共用数据库

可在一个位置找到所有池设置：“配置池”边栏选项卡。 若要转到该位置，请在门户中找到所需的弹性池，然后在边栏选项卡顶部，或者在左侧资源菜单中单击“配置池”。

在此处，可以进行以下任意更改组合，并将所有这些更改保存到一个批处理文件中：

1. 更改池的服务层
2. 缩放性能（DTU 或 vCore）和存储
3. 在池中添加或删除数据库
4. 为池中的数据库设置最小（保证）和最大性能限制
5. 检查成本摘要，以查看做出新选择后帐单发生的任何更改

![弹性池配置边栏选项卡](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell：管理弹性池和共用数据库

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库，但未来的所有开发都不适用于 Az.Sql 模块。 有关这些 cmdlet，请参阅[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 命令在 Az 模块和 AzureRm 模块中的参数是大体上相同的。

若要使用 Azure PowerShell 创建并管理 SQL 数据库弹性池和入池数据库，请使用以下 PowerShell cmdlet。 如果需要安装或升级 PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 若要创建和管理弹性池的 SQL 数据库服务器，请参阅[创建和管理 SQL 数据库服务器](sql-database-servers.md)。 若要创建和管理防火墙规则，请参阅[使用 PowerShell 创建和管理防火墙规则](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-azure-powershell)。

> [!TIP]
> 有关 PowerShell 示例脚本，请参阅[使用 PowerShell 创建弹性池、在池之间移动数据库以及将数据库移出池](scripts/sql-database-move-database-between-pools-powershell.md)和[使用 PowerShell 监视和缩放 Azure SQL 数据库中的 SQL 弹性池](scripts/sql-database-monitor-and-scale-pool-powershell.md)。
>

| Cmdlet | 描述 |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|创建弹性池。|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|获取弹性池及其属性值。|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|修改弹性池的属性。例如，使用“StorageMB”属性修改弹性池的最大存储。|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|删除弹性池。|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|获取弹性池的运行状态|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|在现有池中创建新数据库或将其创建为单一数据库。 |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|获取一个或多个数据库。|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|设置数据库的属性，将现有数据库移入、移出弹性池或在其之间移动。|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|删除数据库。|

> [!TIP]
> 使用门户或每次只能创建一个单一数据库的 PowerShell cmdlet 在弹性池中创建多个数据库可能需要一段时间。 若要自动创建到弹性池中，请参阅 [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)。

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI：管理弹性池和共用数据库

若要使用 [Azure CLI](/cli/azure) 创建和管理 SQL 数据库弹性池，请使用下面的 [Azure CLI SQL 数据库](/cli/azure/sql/db)命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在浏览器中运行 CLI，或者在 macOS、Linux 或 Windows 上[安装](/cli/azure/install-azure-cli)它。

> [!TIP]
> 有关 Azure CLI 示例脚本，请参阅[使用 CLI 移动 SQL 弹性池中的 Azure SQL 数据库](scripts/sql-database-move-database-between-pools-cli.md)和[使用 Azure CLI 缩放 Azure SQL 数据库中的 SQL 弹性池](scripts/sql-database-scale-pool-cli.md)。
>

| Cmdlet | 描述 |
| --- | --- |
|[az sql 弹性池创建](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|创建弹性池。|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|返回服务器中弹性池的列表。|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|返回弹性池中数据库的列表。|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|还包括可用的池 DTU 设置、存储限制和每数据库设置。 为了降低冗长，默认情况下会隐藏附加的存储限制以及每数据库设置。|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|更新弹性池。|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|删除弹性池。|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL：管理共用数据库

若要使用 Transact-SQL 在现有弹性池内创建和移动数据库或者返回有关 SQL 数据库弹性池的信息，请使用以下 T-SQL 命令。 可以使用 Azure 门户、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs) 或可以连接到 Azure SQL 数据库服务器并传递 Transact-SQL 命令的其他任何程序，发出这些命令。 若要使用 T-SQL 创建和管理防火墙规则，请参阅[使用 Transact-SQL 管理防火墙规则](sql-database-firewall-configure.md#manage-ip-firewall-rules-using-transact-sql)。

> [!IMPORTANT]
> 无法使用 Transact-SQL 创建、更新或删除 Azure SQL 数据库弹性池。 可以在弹性池中添加或删除数据库，并且可以使用 DMV 返回有关现有弹性池的信息。
>

| 命令 | 描述 |
| --- | --- |
|[CREATE DATABASE （Azure SQL 数据库）](/sql/t-sql/statements/create-database-azure-sql-database)|在现有池中创建新数据库或将其创建为单一数据库。 必须连接到 master 数据库，才能新建数据库。|
| [ALTER DATABASE （Azure SQL 数据库）](/sql/t-sql/statements/alter-database-azure-sql-database) |将数据库移入、移出弹性池或在其之间移动。|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|删除数据库。|
|[sys.elastic_pool_resource_stats （Azure SQL 数据库）](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|返回 SQL 数据库服务器中所有弹性池的资源使用率统计信息。 对于每个弹性池，报告窗口每 15 秒就会提供一行（每分钟四行）。 这包括池中所有数据库的 CPU、IO、日志和存储消耗以及并发的请求/会话利用率。|
|[sys.database_service_objectives （Azure SQL 数据库）](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|返回 Azure SQL 数据库或 Azure SQL 数据仓库的版本（服务层）、服务目标（定价层）和弹性池名称（若有）。 如果已登录 Azure SQL 数据库服务器中的 master 数据库，返回所有数据库的相关信息。 对于 Azure SQL 数据仓库，必须连接到 master 数据库。|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API：管理弹性池和共用数据库

若要创建和管理 SQL 数据库弹性池和入池数据库，请使用这些 REST API 请求。

| 命令 | 描述 |
| --- | --- |
|[弹性池-创建或更新](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|创建新弹性池或更新现有的弹性池。|
|[弹性池-删除](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|删除弹性池。|
|[弹性池-获取](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|获取弹性池。|
|[弹性池-按服务器列出](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|返回服务器中弹性池的列表。|
|[弹性池-更新](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|更新现有的弹性池。|
|[弹性池活动](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|返回弹性池活动。|
|[弹性池数据库活动](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|返回弹性池内数据库上的活动。|
|[数据库-创建或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|创建新数据库或更新现有数据库。|
|[数据库-获取](https://docs.microsoft.com/rest/api/sql/databases/get)|获取数据库。|
|[数据库-按弹性池列出](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|返回弹性池中数据库的列表。|
|[数据库-按服务器列出](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|返回服务器中的数据库列表。|
|[数据库-更新](https://docs.microsoft.com/rest/api/sql/databases/update)|更新现有的数据库。|

## <a name="next-steps"></a>后续步骤

* 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅 [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。
* 有关使用弹性池的 SaaS 教程，请参阅 [Wingtip SaaS 应用程序简介](sql-database-wtp-overview.md)。
