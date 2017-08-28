---
title: "Azure SQL 数据库服务和性能层 | Microsoft Docs"
description: "比较单一数据库的 SQL 数据库服务层和性能级别，并介绍 SQL 弹性池"
keywords: "数据库选项,数据库性能"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/30/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: b25ff5331f119efd44c61808f7d1d5decb226bd6
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---
# <a name="what-performance-options-are-available-for-an-azure-sql-database"></a>针对 Azure SQL 数据库提供了哪些性能选项

[Azure SQL 数据库](sql-database-technical-overview.md)针对单一数据库和[池化](sql-database-elastic-pool.md)数据库都提供了四个服务层。 这些服务层为：**基本**、**标准**、**高级**和**高级 RS**。 在每个服务层内有多个性能级别 ([DTU](sql-database-what-is-a-dtu.md)) 和存储选项用来处理不同的工作负荷和数据大小。 更高的性能级别提供更多的计算和存储资源，旨在逐级提高吞吐量和容量。 可在不停机的情况下动态更改服务层、性能级别和存储。 
- **基本**、**标准**和**高级**服务层都提供 99.99% 的运行时间 SLA、灵活的业务连续性选项、安全功能和按小时计费功能。 
- **高级 RS** 层提供与高级层相同的性能级别，但具有降低的 SLA，因为与其他服务层中的数据库相比，它运行较少的冗余副本。 因此，在发生服务故障时，可能需要从备份中恢复数据库，这最长会出现 5 分钟的滞后时间。

> [!IMPORTANT]
> Azure SQL 数据库可确保获得一组资源，并且数据库的预期性能特征不受 Azure 中的任何其他数据库影响。 

## <a name="choosing-a-service-tier"></a>选择服务层
下表提供了最适用于不同应用程序工作负荷的层的示例。

| 服务层 | 目标工作负荷 |
| :--- | --- |
| **基本** | 最适合小型数据库，通常支持在给定时间执行一个活动操作。 示例包括用于开发或测试的数据库，或不常使用的小型应用程序。 |
| **标准** |云应用程序的首选选项具有低到中等的 IO 性能要求，支持多个并发查询。 示例包括工作组或 Web 应用程序。 |
| **高级** | 专为具有高 IO 性能要求的高事务量设计，支持多个并发用户。 示例包括支持任务关键型应用程序的数据库。 |
| **高级 RS** | 专为不需要最高可用性保证的 IO 密集型工作负荷设计。 示例包括测试高性能工作负荷或数据库不是记录系统的分析工作负荷。 |
|||

可以使用服务层中的专用资源创建处于特定[性能级别](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)的单一数据库，也可以在 [SQL 弹性池](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)中创建数据库。 在 SQL 弹性池中，计算和存储资源是在单一逻辑服务器中的多个数据库之间共享的。 

可用于单一数据库的资源以数据库事务单位 (DTU) 表示，可用于 SQL 弹性池的资源则以弹性数据库事务单位 (eDTU) 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU 和 eDTU？](sql-database-what-is-a-dtu.md)

若要确定服务层，首先确定需要的最少数据库功能：

| **服务层功能** | **基本** | **标准** | **高级** | **高级 RS**|
| :-- | --: | --: | --: | --: |
| 最大单一数据库大小 | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| 最大的弹性池大小 | 156 GB | 2.9 TB | 4 TB* | 750 GB |
| 弹性池中的最大数据库大小 | 2 GB | 250 GB | 500 GB | 500 GB |
| 每个池的数据库数目上限 | 500  | 500 | 100 | 100 |
| 最大单一数据库 DTU | 5 | 100 | 4000 | 1000 |
| 弹性池中的每数据库最大 DTU | 5 | 3000 | 4000 | 1000 |
| 数据库备份保留期 | 7 天 | 35 天 | 35 天 | 35 天 |
||||||

> [!IMPORTANT]
> 最多存储 4 TB，目前在以下区域中可用：美国东部 2、美国西部、美国弗吉尼亚州政府、西欧、德国中部、东南亚、日本东部、澳大利亚东部、加拿大中部和加拿大东部。 请参阅[当前的 4 TB 限制](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

确定了合适的服务层后，就可以确定数据库的性能级别（DTU 数）和存储量。 

- 通常情况下，开始时适合使用**标准**层中的 S2 和 S3 性能级别。 
- 对于具有高 CPU 或 IO 要求的数据库，开始时适合使用**高级**层中的性能级别。 
- **高级**层提供更多的 CPU，并且一开始就提供比**标准**层中的最高性能级别高出 10 倍的 IO。
- **高级 RS** 层以较低的成本提供**高级**层的性能，但具有降低的 SLA。

> [!IMPORTANT]
> 有关将数据库分组到 SQL 弹性池中以共享计算和存储资源的详细信息，请查看 [SQL 弹性池](sql-database-elastic-pool.md)主题。 本主题的其余部分侧重介绍了单一数据库的服务层和性能级别。
>

## <a name="single-database-service-tiers-and-performance-levels"></a>单一数据库服务层和性能级别
对于单一数据库，每个服务层内都有多个性能级别和存储量。 

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="scaling-up-or-scaling-down-a-single-database"></a>上下缩放单一数据库

在最初选择服务层和性能级别之后，可根据实际经验动态地上下缩放单一数据库。  

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

更改数据库的服务层和/或性能级别会在新的性能级别创建原始数据库的副本，然后将连接切换到副本。 当我们切换到副本时，在此过程中不会丢失任何数据，但在短暂的瞬间，将禁用与数据库的连接，因此可能回滚某些处于进行状态的事务。 用于切换的时间长度因情况而异，但通常为 4 秒以下，并且 99% 的情况下少于 30 秒。 如果在禁用连接的那一刻有大量的事务正在进行，则用于切换的时间长度可能会更长。  

整个扩展过程的持续时间同时取决于更改前后数据库的大小和服务层。 例如，一个正在更改到标准服务层、从标准服务层更改或在标准服务层内更改的 250 GB 的数据库应在 6 小时内完成。 对于与正在高级服务层内更改性能级别的大小相同的数据库，它应在 3 小时内完成。

> [!TIP]
> 若要检查正在进行的 SQL 数据库缩放操作的状态，可以使用以下查询：```select * from sys.dm_operation_status```。
>

* 如果要升级到较高的服务层或性能级别，除非显式指定了较大的最大大小，否则，最大数据库大小不会增大。
* 若要对数据库进行降级，数据库必须小于目标服务层允许的最大大小。 
* 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下升级数据库时，请先将辅助数据库升级到所需的性能层，然后再升级主数据库（一般原则）。 在升级到另一版本时，必须首先升级辅助数据库。 
* 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下降级数据库时，请先将主数据库降级到所需的性能层，然后再降级辅助数据库（一般原则）。 在降级到另一版本时，必须首先降级主数据库。 

* 各服务层提供的还原服务各不相同。 如果要降级到**基本**层，你将具有更小的备份保留期 - 请参阅 [Azure SQL 数据库备份](sql-database-automated-backups.md)。
* 所做的更改完成之前不会应用数据库的新属性。


## <a name="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize"></a>最大大小为 4 TB 的 P11 和 P15 数据库的当前限制

在某些区域中，对于 P11 和 P15 A 数据库，支持的最大大小为 4 TB（如前面所讨论）。 对于最大大小为 4 TB 的 P11 和 P15 数据库，存在以下注意事项和限制：

- 如果在创建数据库时选择 4 TB 最大大小选项（使用值 4 TB 或 4096 GB），如果在不受支持的区域中预配数据库，则 create 命令将会失败并出错。
- 对于位于一个受支持区域中的 P11 和 P15 数据库，可将最大存储大小增加到 4 TB。 可以使用 [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) 或通过在 Azure 门户中查看数据库大小来检查此项内容。 升级现有 P11 或 P15 数据库只能由服务器级主体登录名或 dbmanager 数据库角色的成员执行。 
- 如果在受支持的区域中执行升级操作，则配置会立即更新。 在升级过程中，数据库将保持联机。 但是，在实际的数据库文件已升级到新的最大大小之前，无法利用完整的 4 TB 存储。 所需的时间长度取决于要升级的数据库的大小。  
- 创建或更新 P11 或 P15 数据库时，只能在 1 TB 和 4 TB 最大大小之间选择。 中间存储大小当前不受支持。 创建 P11/P15 数据库时，系统已预先选择了默认的 1TB 存储选项。 对于位于某个受支持区域中的数据库，可将新的或现有单一数据库的存储上限增加到 4TB。 对于所有其他区域，无法将最大大小增大到 1 TB 以上。 选择 4 TB 的随附存储时，价格不会更改。
- 即使使用的实际存储低于 1 TB，也不能将 4 TB 的数据库最大大小更改为 1 TB。 因此，不能将 P11-4TB/P15-4TB 降级到 P11-1TB/P15-1TB 或更低的性能层（例如，降级到 P1-P6），除非我们为其余性能层提供了更多存储选项。 此限制也适用于还原和复制方案，包括时间点、异地还原、长期备份保留以及数据库复制。 数据库配置了 4 TB 选项后，此数据库的所有还原操作都必须适合最大大小为 4 TB 的 P11/P15。
- 在不受支持的区域中创建或升级 P11/P15 数据库时，创建或升级操作会失败，并出现以下错误消息：**最多带有 4 TB 存储的 P11 和 P15 数据库在美国东部 2、美国西部、美国弗吉尼亚州政府、西欧、德国中部、东南亚、日本东部、澳大利亚东部、加拿大中部和加拿大东部可用。**
- 对于“活动异地复制”方案：
   - 设置异地复制关系：如果主数据库是 P11 或 P15，则辅助数据库也必须为 P11 或 P15，更低的性能层会被拒绝作为辅助数据库，因为它们不能支持 4 TB。
   - 升级异地复制关系中的主数据库：在主数据库上将最大大小更改到 4 TB 将触发辅助数据库上的相同更改。 这两个升级都必须成功才能使主数据库上的更改生效。 4TB 选项的区域限制适用（请参阅上文）。 如果辅助数据库位于不支持 4 TB 的区域，则不会升级主数据库。
- 将导入/导出服务用于加载 P11-4TB/P15-4TB 数据库不受支持。 使用 SqlPackage.exe 可[导入](sql-database-import.md)和[导出](sql-database-export.md)数据。

## <a name="manage-single-database-service-tiers-and-performance-levels-using-the-azure-portal"></a>使用 Azure 门户管理单一数据库服务层和性能级别

若要使用 Azure 门户为新的或现有 Azure SQL 数据库设置或更改服务层、性能级别或存储量，请单击“定价层(缩放 DTU)”打开数据库的“配置性能”窗口 - 如以下屏幕截图中所示。 

- 通过为你的工作负荷选择服务层来设置或更改服务层。 
- 使用“DTU”滑块在服务层内设置或更改性能级别 (**DTU**)。
- 使用“存储”滑块为性能级别设置或更改存储量。 

  ![配置服务层和性能级别](./media/sql-database-service-tiers/service-tier-performance-level.png)

> [!IMPORTANT]
> 当选择 P11 或 P15 服务层时，请查看[最大大小为 4 TB 的 P11 和 P15 数据库的当前限制](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)。
>

## <a name="manage-single-database-service-tiers-and-performance-levels-using-powershell"></a>使用 PowerShell 管理单一数据库服务层和性能级别

若要使用 PowerShell 设置或更改 Azure SQL 数据库服务层、性能级别和存储量，请使用以下 PowerShell cmdlet。 如果需要安装或升级 PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 

| Cmdlet | 说明 |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|创建数据库 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|获取一个或多个数据库|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|设置数据库的属性，或者将现有数据库移动到弹性池中|


> [!TIP]
> 可以使用 PowerShell 脚本监视数据库的性能指标，将其缩放到更高的性能级别，并基于性能指标之一创建警报规则，有关这样的示例脚本，请参阅[使用 PowerShell 监视和缩放单一 SQL 数据库](scripts/sql-database-monitor-and-scale-database-powershell.md)。

## <a name="manage-single-database-service-tiers-and-performance-levels-using-the-azure-cli"></a>使用 Azure CLI 管理单一数据库服务层和性能级别

若要使用 Azure CLI 设置或更改 Azure SQL 数据库服务层、性能级别和存储量，请使用以下 [Azure CLI SQL 数据库](/cli/azure/sql/db)命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在浏览器中运行 CLI，或者在 macOS、Linux 或 Windows 上[安装](/cli/azure/install-azure-cli)它。 若要创建并管理 SQL 弹性池，请参阅[弹性池](sql-database-elastic-pool.md)。

| Cmdlet | 说明 |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#create) |创建数据库|
|[az sql db list](/cli/azure/sql/db#list)|列出某台服务器中的所有数据库和数据仓库，或者列出弹性池中的所有数据库|
|[az sql db list-editions](/cli/azure/sql/db#list-editions)|列出可用的服务目标和存储上限|
|[az sql db list-usages](/cli/azure/sql/db#list-usages)|返回数据库使用情况|
|[az sql db show](/cli/azure/sql/db#show)|获取数据库或数据仓库|
|[az sql db update](/cli/azure/sql/db#update)|更新数据库|

> [!TIP]
> 可以使用 Azure CLI 脚本在查询数据库的大小信息后将单一 Azure SQL 数据库缩放到不同的性能级别，有关这样的示例脚本，请参阅[使用 CLI 监视和缩放单一 SQL 数据库](scripts/sql-database-monitor-and-scale-database-cli.md)。
>

## <a name="manage-single-database-service-tiers-and-performance-levels-using-transact-sql"></a>使用 Transact-SQL 管理单一数据库服务层和性能级别

若要使用 Transact-SQL 设置或更改 Azure SQL 数据库服务层、性能级别和存储量，请使用以下 T-SQL 命令。 可以使用 Azure 门户、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs) 或可以连接到 Azure SQL 数据库服务器并传递 Transact-SQL 命令的任何其他程序来发出这些命令。 

| 命令 | 说明 |
| --- | --- |
|[CREATE DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/create-database-azure-sql-database)|新建数据库。 必须连接到 master 数据库，才能新建数据库。|
| [ALTER DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/alter-database-azure-sql-database) |修改 Azure SQL 数据库。 |
|[sys.database_service_objectives（Azure SQL 数据库）](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|返回 Azure SQL 数据库或 Azure SQL 数据仓库的版本（服务层）、服务目标（定价层）和弹性池名称（若有）。 如果已登录 Azure SQL 数据库服务器中的 master 数据库，返回所有数据库的相关信息。 对于 Azure SQL 数据仓库，必须连接到 master 数据库。|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|列出 Azure SQL 数据库服务器上的数据库的数目、类型和持续时间。|

以下示例演示如何使用 ALTER DATABASE 命令更改最大大小：

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-databases-using-the-rest-api"></a>使用 REST API 管理单一数据库

若要使用 REST API 设置或更改 Azure SQL 数据库服务层、性能级别和存储量，请参阅 [Azure SQL 数据库 REST API](/rest/api/sql/)。

## <a name="next-steps"></a>后续步骤

* 了解有关 [DTU](sql-database-what-is-a-dtu.md) 的详细信息。
* 若要了解有关监视 DTU 使用情况的信息，请参阅[监视和性能优化](sql-database-troubleshoot-performance.md)。


