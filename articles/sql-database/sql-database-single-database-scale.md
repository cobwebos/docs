---
title: 缩放单一数据库资源 - Azure SQL 数据库 | Microsoft Docs
description: 本文介绍如何在 Azure SQL 数据库中缩放适用于单一数据库的计算和存储资源。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 525416506a22f386de574ca02b2e919ac47b8737
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311131"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>在 Azure SQL 数据库中缩放单一数据库资源

本文介绍如何在 Azure SQL 数据库中缩放适用于单一数据库的计算和存储资源。 

## <a name="vcore-based-purchasing-model-change-storage-size"></a>基于 vCore 的购买模型：更改存储大小

- 可以使用 1GB 作为增量，将存储预配到最大大小限制。 最小可配置数据存储为 5GB 
- 可通过 [Azure 门户](https://portal.azure.com)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update) 为单一数据库增加或减少大小上限，以预配存储。
- SQL 数据库会自动为日志文件额外分配 30% 的存储，并为 TempDB 的每个 vCore 分配 32GB，但不会超过 384GB。 TempDB 位于所有服务层中的附加 SSD 上。
- 单一数据库的存储价格等于数据存储与日志存储量之和乘以服务层的存储单价。 vCore 价格已包括 TempDB 费用。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="vcore-based-purchasing-model-change-compute-resources"></a>基于 vCore 的购买模型：更改计算资源

最初选择 vCore 数量后，可以使用 [Azure 门户](sql-database-single-database-scale.md#azure-portal-manage-logical-servers-and-databases)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、 [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update)，根据实际体验动态扩展或缩减单一数据库。 

更改数据库的服务层和/或性能级别将在新的性能级别创建原始数据库的副本，并将连接切换到副本。 当我们切换到副本时，在此过程中不会丢失任何数据，但在短暂的瞬间，将禁用与数据库的连接，因此可能回滚某些处于进行状态的事务。 用于切换的时间长度因情况而异，但通常为 4 秒以下，并且 99% 的情况下少于 30 秒。 如果在禁用连接的那一刻有大量的事务正在进行，则用于切换的时间长度可能会更长。 

整个扩展过程的持续时间同时取决于更改前后数据库的大小和服务层。 例如，一个正在更改到标准服务层、从“常规用途”服务层更改或在标准服务层内更改的 250 GB 的数据库应在六小时内完成。 如果数据库与正在“业务关键”服务层内更改性能级别的大小相同，应在三小时内完成扩展。

> [!TIP]
> 若要监视正在进行的操作，请参阅：[使用 SQL REST API 管理操作](/rest/api/sql/Operations/List)、[使用 CLI 管理操作](/cli/azure/sql/db/op)、[使用 T-SQL 管理操作](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)及以下两个 PowerShell 命令：[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) 和 [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)。

* 如果要升级到更高的服务层或性能级别，除非显式指定了更大的大小（最大），否则，最大数据库大小不会增大。
* 若要对数据库进行降级，数据库所用空间必须小于目标服务层和性能级别允许的最大大小。 
* 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下升级数据库时，请先将辅助数据库升级到所需的性能层，然后再升级主数据库（为获得最佳性能的常规指南）。 在升级到另一版本时，必须首先升级辅助数据库。
* 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下降级数据库时，请先将主数据库降级到所需的性能层，然后再降级辅助数据库（为获得最佳性能的常规指南）。 在降级到另一版本时，必须首先降级主数据库。
* 所做的更改完成之前不会应用数据库的新属性。

## <a name="dtu-based-purchasing-model-change-storage-size"></a>基于 DTU 的购买模型：更改存储大小

- 单一数据库的 DTU 价格附送了一定容量的存储，无需额外费用。 超出附送的量后，可花费额外的费用预配额外的存储，但不能超过存储上限，不超过 1 TB 时，以 250 GB 为增量进行预配，超出 1 TB 时，以 256 GB 为增量进行预配。 有关附送存储量和大小上限，请参阅[单一数据库：存储大小和性能级别](#single-database-storage-sizes-and-performance-levels)。
- 可通过 [Azure portal](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update) 为单一数据库增加大小上限，以预配额外存储。
- 单一数据库的额外存储价格等于额外存储量乘以服务层的额外存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="dtu-based-purchasing-model-change-compute-resources-dtus"></a>基于 DTU 的购买模型：更改计算资源 (DTU)

首先选择服务层、性能级别和存储量，然后使用 [Azure portal](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、 [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update)，根据实际体验动态扩展或缩减单一数据库。 

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

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>基于 DTU 的购买模型：当最大大小超过 1 TB 时，P11 和 P15 的限制

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


## <a name="next-steps"></a>后续步骤

有关总体资源限制，请参阅 [SQL 数据库基于 vCore 的资源限制 - 单一数据库](sql-database-vcore-resource-limits-single-databases.md)和 [SQL 数据库基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-single-databases.md)。
