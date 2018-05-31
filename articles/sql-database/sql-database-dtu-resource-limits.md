---
title: Azure SQL 数据库基于 DTU 的资源限制 | Microsoft Docs
description: 本页介绍 Azure SQL 数据库的一些常见基于 DTU 的资源限制。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: fb5c2e16e696ba9eecf4346a0c4e7bc05aacf39f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32194741"
---
# <a name="azure-sql-database-dtu-based-resource-model-limits"></a>Azure SQL 数据库基于 DTU 的资源模型限制

> [!IMPORTANT]
> 有关基于 vCore 的资源限制，请参阅 [SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits.md)。

## <a name="single-database-storage-sizes-and-performance-levels"></a>单一数据库：存储大小和性能级别

对于单一数据库，下表显示了可用于每个服务层和性能级别的单一数据库的资源。 可通过 [Azure 门户](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal)、[Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql)、[PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell)、[Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) 或 [REST API](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api) 为单一数据库设置服务层、性能级别和存储量。

### <a name="basic-service-tier"></a>基本服务层
| **性能级别** | **基本** |
| :--- | --: |
| 最大 DTU 数 | 5 |
| 包含的存储 (GB) | 2 |
| 最大存储选择 (GB) | 2 |
| 最大内存中 OLTP 存储 (GB) |不适用 |
| 最大并发工作线程数（请求数） | 30 |
| 最大并发会话数 | 300 |
|||

### <a name="standard-service-tier"></a>标准服务层
| **性能级别** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU 数 | 10 | 20 | 50 | 100 |
| 包含的存储 (GB) | 250 | 250 | 250 | 250 |
| 最大存储选择 (GB)* | 250 | 250 | 250 | 250, 500, 750, 1024 |
| 最大内存中 OLTP 存储 (GB) | 不适用 | 不适用 | 不适用 | 不适用 |
| 最大并发工作线程数（请求数）| 60 | 90 | 120 | 200 |
| 最大并发会话数 |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>标准服务层（续）
| **性能级别** | **S4** | **S6** | S7 | S9 | S12 |
| :--- |---:| ---:|---:|---:|---:|---:|
| 最大 DTU 数 | 200 | 400 | 800 | 1600 | 3000 |
| 包含的存储 (GB) | 250 | 250 | 250 | 250 | 250 |
| 最大存储选择 (GB)* | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| 最大内存中 OLTP 存储 (GB) | 不适用 | 不适用 | 不适用 | 不适用 |不适用 |
| 最大并发工作线程数（请求数）| 400 | 800 | 1600 | 3200 |6000 |
| 最大并发会话数 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>高级服务层 
| **性能级别** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU 数 | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 包含的存储 (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| 最大存储选择 (GB)* | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| 最大内存中 OLTP 存储 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 最大并发工作线程数（请求数）| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 最大并发会话数 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> - 超出所包括存储量的存储大小为预览版，需额外付费。 有关详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 
>
> - 在高级层中，以下区域目前提供的存储超出 1 TB：澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、美国中部、法国中部、德国中部、日本东部、日本西部、韩国中部、美国中北部、北欧、美国中南部、东南亚、英国南部、英国西部、美国东部 2、美国西部、US Gov 弗吉尼亚州和西欧。 请参阅 [P11-P15 当前限制](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
> 


## <a name="single-database-change-storage-size"></a>单一数据库：更改存储大小

- 单一数据库的 DTU 价格附送了一定容量的存储，无需额外费用。 超出附送的量后，可花费额外的费用预配额外的存储，但不能超过存储上限，不超过 1 TB 时，以 250 GB 为增量进行预配，超出 1 TB 时，以 256 GB 为增量进行预配。 有关附送存储量和大小上限，请参阅[单一数据库：存储大小和性能级别](#single-database-storage-sizes-and-performance-levels)。
- 可通过 [Azure portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update) 为单一数据库增加大小上限，以预配额外存储。
- 单一数据库的额外存储价格等于额外存储量乘以服务层的额外存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="single-database-change-dtus"></a>单一数据库：更改 DTU

首先选择服务层、性能级别和存储量，然后使用 [Azure portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、 [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update)，根据实际体验动态扩展或缩减单一数据库。 

下面视频演示了如何动态更改性能层以增加单一数据库的可用 DTU。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

更改数据库的服务层和/或性能级别将在新的性能级别创建原始数据库的副本，并将连接切换到副本。 当我们切换到副本时，在此过程中不会丢失任何数据，但在短暂的瞬间，将禁用与数据库的连接，因此可能回滚某些处于进行状态的事务。 用于切换的时间长度因情况而异，但 99% 的情况下少于 30 秒。 如果在禁用连接的那一刻有大量的事务正在进行，则用于切换的时间长度可能会更长。 

整个扩展过程的持续时间同时取决于更改前后数据库的大小和服务层。 例如，一个正在更改到标准服务层、从标准服务层更改或在标准服务层内更改的 250 GB 的数据库应在六小时内完成。 如果数据库与正在高级服务层内更改性能级别的大小相同，应在三小时内完成扩展。

> [!TIP]
> 若要监视正在进行的操作，请参阅：[使用 SQL REST API 管理操作](/rest/api/sql/Operations/List)、[使用 CLI 管理操作](/cli/azure/sql/db/op)、[使用 T-SQL 管理操作](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)及以下两个 PowerShell 命令：[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) 和 [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)。

* 如果要升级到更高的服务层或性能级别，除非显式指定了更大的大小（最大），否则，最大数据库大小不会增大。
* 若要对数据库进行降级，数据库所用空间必须小于目标服务层和性能级别允许的最大大小。 
* 从高级降级至标准层时，如果同时满足 (1) 目标性能级别支持该数据库的最大大小，(2) 最大大小超出目标性能级别附送的存储量，那么将产生额外存储费用。 例如，如果将最大大小为 500 GB 的 P1 数据库缩小至 S3，那么将产生额外的存储费用，因为 S3 支持的最大大小为 500 GB，而它的附送存储量仅为 250 GB。 因此，额外存储量为 500 GB – 250 GB = 250 GB。 有关额外存储定价的信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 如果实际使用的空间量小于附送的存储量，只要将数据库最大大小减少到附送的量，就能避免此项额外费用。 
* 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下升级数据库时，请先将辅助数据库升级到所需的性能层，然后再升级主数据库（为获得最佳性能的常规指南）。 在升级到另一版本时，必须首先升级辅助数据库。
* 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下降级数据库时，请先将主数据库降级到所需的性能层，然后再降级辅助数据库（为获得最佳性能的常规指南）。 在降级到另一版本时，必须首先降级主数据库。
* 各服务层提供的还原服务各不相同。 如果要降级到基本层，则备份保留期也将减少 - 请参阅 [Azure SQL 数据库备份](sql-database-automated-backups.md)。
* 所做的更改完成之前不会应用数据库的新属性。

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>单一数据库：当最大大小超过 1 TB 时，P11 和 P15 的限制

以下区域支持最大大小超过 1 TB 的 P11 和 P15 数据库：澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、美国中部、法国中部、德国中部、日本东部、日本西部、韩国中部、美国中北部、北欧、美国中南部、东南亚、英国南部、英国西部、美国东部 2、美国西部、美国弗吉尼亚州政府和西欧。 对于最大大小超过 1 TB 的 P11 和 P15 数据库，存在以下注意事项和限制：

- 如果在创建数据库时选择超过 1 TB 的最大大小（使用值 4 TB 或 4096 GB），如果在不受支持的区域中预配数据库，则 create 命令将会失败并出错。
- 对于位于一个受支持区域中的 P11 和 P15 数据库，可以以 256 GB 为增量，将其最大存储增至 1 TB 以上，最高可达 4 TB。 要了解所在区域是否支持更大的大小，请使用 [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) 功能，或在 Azure 门户中查看数据库大小。 升级现有 P11 或 P15 数据库只能由服务器级主体登录名或 dbmanager 数据库角色的成员执行。 
- 如果在受支持的区域中执行升级操作，则配置会立即更新。 在升级过程中，数据库将保持联机。 但是，在实际的数据库文件已升级到新的最大大小之前，无法利用超过 1 TB 的所有存储量。 所需的时间长度取决于要升级的数据库的大小。 
- 创建或更新 P11 或 P15 数据库时，只能在增量为 256 GB 的 1 TB 和 4 TB 最大大小之间选择。 创建 P11/P15 数据库时，系统已预先选择了默认的 1TB 存储选项。 对于位于某个受支持区域中的数据库，可将新的或现有单一数据库的存储上限增加到 4 TB。 对于所有其他区域，无法将最大大小增大到 1 TB 以上。 选择 4 TB 的随附存储时，价格不会更改。
- 如果将数据库的最大大小设置为 1 TB 以上，则即使使用的实际存储不到 1 TB，也无法将其更改为 1 TB。 因此，无法将最大大小超过 1 TB 的 P11 或 P15 降级为 1 TB P11、1 TB P15 或更低的性能层，例如 P1-P6）。 此限制也适用于还原和复制方案，包括时间点、异地还原、长期备份保留以及数据库复制。 将数据库的最大大小配置为超过 1 TB 后，此数据库的所有还原操作都必须适合最大大小高于 1 TB 的 P11/P15。
- 对于“活动异地复制”方案：
   - 设置异地复制关系：如果主数据库是 P11 或 P15，则辅助数据库也必须为 P11 或 P15，更低的性能层会被拒绝作为辅助数据库，因为它们不能支持超过 1 TB。
   - 升级异地复制关系中的主数据库：在主数据库上将最大大小更改为超过 1 TB 将触发辅助数据库上的相同更改。 这两个升级都必须成功才能使主数据库上的更改生效。 超过 1 TB 选项的区域限制适用。 如果辅助数据库位于不支持超过 1 TB 的区域，则不会升级主数据库。
- 不支持将导入/导出服务用于加载超过 1 TB 的 P11/P15 数据库。 使用 SqlPackage.exe 可[导入](sql-database-import.md)和[导出](sql-database-export.md)数据。

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>弹性池：存储大小和性能级别

对于 SQL 数据库弹性池，下表显示了在每个服务层和性能级别可用的资源。 可通过 [Azure 门户](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell)、[Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) 或 [REST API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api) 设置服务层、性能级别和存储量。

> [!NOTE]
> 弹性池中各个数据库的资源限制通常与池外部基于 DTU 和服务层的各个数据库相同。 例如，S2 数据库的最大并发辅助进程数为 120 个。 因此，如果池中每个数据库的最大 DTU 是 50 个 DTU（这等效于 S2），则标准池中数据库的最大并发辅助进程数也是 120 个辅助进程。

### <a name="basic-elastic-pool-limits"></a>基本弹性池限制

| 每个池的 eDTU 数 | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| 每个池包含的存储 (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| 每个池的最大存储选择 (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| 每个池的最大内存中 OLTP 存储 (GB) | 不适用 | 不适用 | 不适用 | 不适用 | 不适用 | 不适用 | 不适用 | 不适用 |
| 每个池的最大数据库数 | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| 每个池的最大并发工作线程数（请求数） | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| 每个池的最大并发会话数 | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| 每个数据库的最小 eDTU 数选择 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| 每个数据库的最大 eDTU 数选择 | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| 每个数据库的最大存储空间 (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>标准弹性池限制

| 每个池的 eDTU 数 | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| 每个池包含的存储 (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| 每个池的最大存储选择 (GB)* | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| 每个池的最大内存中 OLTP 存储 (GB) | 不适用 | 不适用 | 不适用 | 不适用 | 不适用 | 不适用 | 
| 每个池的最大数据库数 | 100 | 200 | 500 | 500 | 500 | 500 | 
| 每个池的最大并发工作线程数（请求数） | 100 | 200 | 400 | 600 | 800 | 1600 |
| 每个池的最大并发会话数 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| 每个数据库的最小 eDTU 数选择 | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| 每个数据库的最大 eDTU 数选择 | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| 每个数据库的最大存储 (GB)* | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>标准弹性池限制（续） 

| 每个池的 eDTU 数 | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| 每个池包含的存储 (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| 每个池的最大存储选择 (GB)* | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| 每个池的最大内存中 OLTP 存储 (GB) | 不适用 | 不适用 | 不适用 | 不适用 | 不适用 | 
| 每个池的最大数据库数 | 500 | 500 | 500 | 500 | 500 | 
| 每个池的最大并发工作线程数（请求数） | 2400 | 3200 | 4000 | 5000 | 6000 |
| 每个池的最大并发会话数 | 30000 | 30000 | 30000 | 30000 | 30000 | 
| 每个数据库的最小 eDTU 数选择 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| 每个数据库的最大 eDTU 数选择 | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| 每个数据库的最大存储选择 (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>高级弹性池限制

| 每个池的 eDTU 数 | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| 每个池包含的存储 (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| 每个池的最大存储选择 (GB)* | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| 每个池的最大内存中 OLTP 存储 (GB) | 1 | 2 | 4 | 10 | 12 | 
| 每个池的最大数据库数 | 50 | 100 | 100 | 100 | 100 | 
| 每个池的最大并发工作线程数（请求数） | 200 | 400 | 800 | 1600 | 2400 | 
| 每个池的最大并发会话数 | 30000 | 30000 | 30000 | 30000 | 30000 | 
| 每个数据库的最小 eDTU 数 | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| 每个数据库的最大 eDTU 数 | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| 每个数据库的最大存储 (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>高级弹性池限制（续） 

| 每个池的 eDTU 数 | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| 每个池包含的存储 (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| 每个池的最大存储选择 (GB)* | 2048 | 2560 | 3072 | 3548 | 4096|
| 每个池的最大内存中 OLTP 存储 (GB) | 16 | 20 | 24 | 28 | 32 |
| 每个池的最大数据库数 | 100 | 100 | 100 | 100 | 100 | 
| 每个池的最大并发工作线程数（请求数） | 3200 | 4000 | 4800 | 5600 | 6400 |
| 每个池的最大并发会话数 | 30000 | 30000 | 30000 | 30000 | 30000 | 
| 每个数据库的最小 eDTU 数选择 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| 每个数据库的最大 eDTU 数选择 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| 每个数据库的最大存储 (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> -  超出所包括存储量的存储大小为预览版，需额外付费。 有关详细信息，请参阅 [SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/)。 超出所包括存储量的存储大小为预览版，需额外付费。 有关详细信息，请参阅 [SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/)。
>
> -  在高级层中，以下区域目前提供的存储超出 1 TB：澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、美国中部、法国中部、德国中部、日本东部、日本西部、韩国中部、美国中北部、北欧、美国中南部、东南亚、英国南部、英国西部、美国东部 2、美国西部、US Gov 弗吉尼亚州和西欧。 请参阅 [P11-P15 当前限制](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
>

如果使用了弹性池的所有 DTU，那么池中的每个数据库将接收相同数量的资源来处理查询。 SQL 数据库服务通过确保相等的计算时间片，在数据库之间提供资源共享的公平性。 弹性池资源共享公平性是在将每个数据库的 DTU 最小值设为非零值时，对另外为每个数据库保证的任意资源量的补充。

### <a name="database-properties-for-pooled-databases"></a>入池数据库的数据库属性

下表介绍了入池数据库的属性。

| 属性 | 说明 |
|:--- |:--- |
| 每个数据库的最大 eDTU 数 |根据池中其他数据库的 eDTU 使用率，池中任何数据库可以使用的 eDTU 的最大数目。 每个数据库的 eDTU 上限并不是数据库的资源保障。 此设置是应用于池中所有数据库的全局设置。 将每个数据库的最大 eDTU 数设置得足够高，以处理数据库使用高峰情况。 因为池通常会假定数据库存在热使用模式和冷使用模式，在这些模式中并非所有数据库同时处于高峰使用状态，所以预期会存在某种程度的过量使用情况。 例如，假设每个数据库的高峰使用量为 20 个 eDTU，并且池中 100 个数据库仅有 20% 同时处于高峰使用中。 如果将每个数据库的 eDTU 最大值设为 20 个 eDTU，则可以认为超量 5 倍使用该池是合理的，并且将每个池的 eDTU 数设为 400。 |
| 每个数据库的最小 eDTU 数 |池中任何数据库可以保证的 eDTU 最小数目。 此设置是应用于池中所有数据库的全局设置。 每个数据库的最小 eDTU 可能设为 0，这也是默认值。 该属性值可以设置为介于 0 和每个数据库的平均 eDTU 使用量之间的任意值。 池中数据库数目和每个数据库的 eDTU 下限的积不能超过每个池的 eDTU 数。 例如，如果一个池有 20 个数据库，每个数据库的 eDTU 最小值设为 10 个 eDTU，则池的 eDTU 数目必须大于或等于 200 个 eDTU。 |
| 每个数据库的最大存储 |用户为池中的数据库设置的最大数据库大小。 但是，入池数据库共享已分配的池存储。 即使“每数据库”最大总存储 * 设置为大于池的可用总存储 * 空间，所有数据库实际使用的空间总量也不能超出可用的池限制。 最大数据库大小是指数据文件的最大大小，不包括日志文件使用的空间。 |
|||
 
## <a name="elastic-pool-change-storage-size"></a>弹性池：更改存储大小

- 弹性池的 eDTU 价格附送了一定容量的存储，无需额外费用。 超出附送的量后，可花费额外的费用预配额外的存储，但不能超过存储上限，不超过 1 TB 时，以 250 GB 为增量进行预配，超出 1 TB 时，以 256 GB 为增量进行预配。 有关附送存储量和大小上限，请参阅[弹性池：存储大小和性能级别](#elastic-pool-storage-sizes-and-performance-levels)。
- 可通过 [Azure 门户](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 或 [REST API](/rest/api/sql/elasticpools/update) 为弹性池增加大小上限，以预配额外存储。
- 弹性池的额外存储价格等于额外存储量乘以服务层的额外存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="elastic-pool-change-edtus"></a>弹性池：更改 eDTU

可按资源需求，通过 [Azure portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 或 [REST API](/rest/api/sql/elasticpools/update) 增加或减少弹性池可用的资源。

- 重新缩放池 eDTU 时，将暂时停止数据库连接。 此行为与重新缩放单一数据库（而非在池中）的 DTU 时的行为相同。 有关在重新缩放操作执行期间，停止数据库连接的持续时间和影响的详细信息，请参阅[重新缩放单一数据库的 DTU](#single-database-change-storage-size)。 
- 重新缩放池 eDTU 的持续时间取决于池中所有数据库使用的总存储空间量。 一般而言，每 100 GB 重新缩放的平均延迟时间不超过 90 分钟。 例如，如果池中所有数据库使用的总空间为 200 GB，则重新缩放池的预计延迟时间将不超过 3 小时。 对标准层或基本层中的某些事例而言，重新缩放延迟时间可能不超过五分钟，不考虑所用空间量的影响。
- 一般而言，更改每个数据库的最小 eDTU 或最大 eDTU 的持续时间将不超过五分钟。
- 当缩小池 eDTU 时，池所用空间必须小于目标服务层和池 eDTU 所允许的最大大小。
- 当重新缩放池 eDTU 时，如果 (1) 目标池支持池的存储上限，(2) 存储上限超过了目标池附送的存储量，将产生额外存储费用。 例如，如果最大大小为 100 GB 的 100 eDTU 标准池缩小为 50 eDTU 标准池，那么将产生额外存储费用，因为目标池支持的最大大小为 100 GB，其附送的存储量仅为 50 GB。 因此，额外存储量为 100 GB – 50 GB = 50 GB。 有关额外存储定价的信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 如果实际使用的空间量小于附送的存储量，只要将数据库最大大小减少到附送的量，就能避免此项额外费用。 

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>服务器和数据库的最大数目是多少？

| 最大值 | 值 |
| :--- | :--- |
| 每个服务器的数据库数 | 5000 |
| 每个区域每个订阅的服务器数 | 20 |
|||

> [!IMPORTANT]
> 随着数据库的数量接近每个服务器的限制，可能出现以下情况：
> <br> •    针对主数据库运行查询的延迟增加。  这包括资源利用率统计信息的视图，如 sys.resource_stats。
> <br> •    管理操作和呈现涉及枚举服务器中的数据库的门户视点方面的延迟增加。

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>当数据库和弹性池资源到达限制时会如何？

### <a name="compute-dtus-and-edtus"></a>计算（DTU 和 eDTU）

当数据库计算使用率（由 DTU 和 eDTU 计量）变高时，查询的延长时间也会增加，甚至可能出现超时。在上述情况下，服务可能对查询排队，并在资源可用时向查询提供资源以用于执行。
计算使用率变高时，风险缓解选项包括：

- 提升数据库或弹性池的性能级别，向数据库提供更多 DTU 或 eDTU。 请参阅[单一数据库：更改 DUT](#single-database-change-dtus) 和[弹性池：更改 eDTU](#elastic-pool-change-edtus)。
- 优化查询，减少每个查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>存储

当使用的数据库空间到达上限时，将无法进行增加数据大小的数据库插入和更新操作，客户端会收到[错误消息](sql-database-develop-error-messages.md)。 数据库的选择和删除操作不受影响。

空间使用率变高时，风险缓解选项包括：

- 增加数据库或弹性池的大小上限或更改性能级别，以包含更多已添加的存储。 请参阅 [SQL 数据库资源限制](sql-database-dtu-resource-limits.md)。
- 如果数据库在弹性池内，那么可选择将数据库移出弹性池，从而避免与其他数据库共享存储空间。

### <a name="sessions-and-workers-requests"></a>会话和辅助角色（请求） 

会话和辅助角色的数目上限由服务层和性能级别（DTU 和 eDTU）决定。 当到达会话或辅助角色上限时，新的请求将被拒绝，客户端将收到错误消息。 虽然应用程序可以轻松地控制可用的连接数，但并行辅助角色数通常更难以估计和控制。 在负荷高峰期，当数据库资源达到上限，辅助角色由于较长时间运行查询而堆积时，这种情况尤其突出。 

会话或辅助角色使用率变高时，风险缓解选项包括：
- 增加数据库或弹性池的服务层或性能级别。 请参阅[单一数据库：更改存储大小](#single-database-change-storage-size)、[单一数据库：更改 DUT](#single-database-change-dtus)、[弹性池：更改存储大小](#elastic-pool-change-storage-size)和[弹性池：更改 eDTU](#elastic-pool-change-edtus)。
- 如果争用计算资源造成了辅助角色使用率上升，请优化查询，以降低每项查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

## <a name="next-steps"></a>后续步骤

- 有关常见问题的解答，请参阅 [SQL 数据库常见问题解答](sql-database-faq.md)。
- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md)。
- 有关 DTU 和 eDTU 的信息，请参阅 [DTU 和 eDTU](sql-database-what-is-a-dtu.md)。
- 有关 tempdb 大小限制的信息，请参阅 https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database。
