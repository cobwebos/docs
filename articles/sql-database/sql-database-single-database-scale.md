---
title: 缩放单一数据库资源
description: 本文介绍如何在 Azure SQL 数据库中缩放适用于单一数据库的计算和存储资源。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: e23a4c39f93ea4de7f5dd38bb266d63ed52913cb
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845860"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>在 Azure SQL 数据库中缩放单一数据库资源

本文介绍如何在预配的计算层中缩放适用于 Azure SQL 数据库的计算和存储资源。 或者，[无服务器计算层](sql-database-serverless.md)为使用的计算提供计算自动缩放和每秒的帐单。

最初选取 Vcore 或 Dtu 数后，可以根据使用[Azure 门户](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、 [transact-sql](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、 [PowerShell](/powershell/module/az.sql/set-azsqldatabase)、 [Azure CLI](/cli/azure/sql/db#az-sql-db-update)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/update)的实际经验，动态缩放单个数据库。

下面的视频演示了如何动态更改服务层级和计算大小以增加单一数据库的可用 DTU。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="impact"></a>影响

更改服务层或计算大小主要涉及服务执行以下步骤：

1. 为数据库创建新的计算实例  

    使用请求的服务层和计算大小创建一个新的计算实例。 对于某些服务层和计算大小更改的组合，必须在新的计算实例中创建数据库的副本，这涉及复制数据，并可能会影响总体延迟。 无论如何，在此步骤中数据库仍保持联机状态，并且连接将继续定向到原始计算实例中的数据库。

2. 将连接的路由切换到新的计算实例

    将删除与原始计算实例中的数据库的现有连接。 将在新的计算实例中建立与数据库的任何新连接。 对于服务层和计算大小更改的某些组合，数据库文件在交换机中分离并重新附加。  无论如何，如果数据库不可用的时间通常不超过30秒，通常只需几秒钟，此开关就会导致短暂的服务中断。 如果在删除连接时运行了长时间运行的事务，则此步骤的持续时间可能需要更长的时间才能恢复中止的事务。 [加速数据库恢复](sql-database-accelerated-database-recovery.md)可以降低中止长时间运行事务的影响。

> [!IMPORTANT]
> 工作流中的任何步骤都不会丢失任何数据。 请确保在服务层更改时，使用 Azure SQL 数据库的应用程序和组件中已实现了一些[重试逻辑](sql-database-connectivity-issues.md)。

## <a name="latency"></a>延迟 

更改服务层或缩放单一数据库或弹性池的计算大小的估计延迟如下：

|服务层|基本单一数据库，</br>标准（S0-S1）|基本弹性池，</br>标准（S2-S12）， </br>超大规模 </br>常规用途单一数据库或弹性池|高级或业务关键单一数据库或弹性池|
|:---|:---|:---|:---|
|**基本单一数据库，</br> 标准（S0-S1）**|与所用空间无关 &bull; &nbsp;固定时间延迟</br>&bull; &nbsp;通常不到5分钟|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|
|**基本弹性池，</br>标准（S2-S12）、</br>超大规模 </br>常规用途单一数据库或弹性池**|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|与所用空间无关 &bull; &nbsp;固定时间延迟</br>&bull; &nbsp;通常不到5分钟|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|
|**高级或业务关键单一数据库或弹性池**|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|

> [!TIP]
> 若要监视正在进行的操作，请参阅：[使用 SQL REST API 管理操作](https://docs.microsoft.com/rest/api/sql/operations/list)、[使用 CLI 管理操作](/cli/azure/sql/db/op)、[使用 t-sql 监视操作](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)和以下两个 PowerShell 命令： [AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity)和[AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

## <a name="cancelling-changes"></a>正在取消更改

可以取消服务层更改或计算重新缩放操作。

#### <a name="azure-portal"></a>Azure 门户

在 "数据库概述" 边栏选项卡中，导航到 "**通知**"，然后单击指示正在进行的操作的磁贴：

![正在进行的操作](media/sql-database-single-database-scale/ongoing-operations.png)

接下来，单击标签为 "**取消此操作**" 的按钮。

![取消正在进行的操作](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

在 PowerShell 命令提示符下，将 `$resourceGroupName`、`$serverName`和 `$databaseName`，并运行以下命令：

```powershell
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>其他注意事项

- 如果要升级到更高的服务层级或计算大小，除非显式指定了更大的大小（最大），否则，最大数据库大小不会增大。
- 若要对数据库进行降级，数据库所用空间必须小于目标服务层级和计算大小允许的最大大小。
- 从“高级”层级降级至“标准”层级时，如果同时满足 (1) 目标计算大小支持该数据库的最大大小，(2) 最大大小超出目标计算大小包括的存储量，那么将产生额外存储费用。 例如，如果将最大大小为 500 GB 的 P1 数据库缩小到 S3，则额外的存储开销会因为 S3 支持最大大小为 1 TB，其包含的存储量仅为 250 GB。 因此，额外存储量为 500 GB – 250 GB = 250 GB。 有关额外存储定价的信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 如果实际使用的空间量小于附送的存储量，只要将数据库最大大小减少到附送的量，就能避免此项额外费用。
- 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下升级数据库时，请先将其辅助数据库升级到所需的服务层级和计算大小，然后再升级主数据库（用于实现最佳性能的常规指南）。 在升级到另一版本时，必须首先升级辅助数据库。
- 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下降级数据库时，请先将其主数据库降级到所需的服务层级和计算大小，然后再降级辅助数据库（用于实现最佳性能的常规指南）。 在降级到另一版本时，必须首先降级主数据库。
- 各服务层级的还原服务不同。 如果要降级到基本层，则备份保持期也将缩短。 请参阅 [Azure SQL 数据库备份](sql-database-automated-backups.md)。
- 所做的更改完成之前不会应用数据库的新属性。

## <a name="billing"></a>计费 

将根据使用最高服务层级的数据库存在的每个小时 + 在该小时适用的计算大小进行计费，无论使用方式或数据库处于活动状态是否少于一小时。 例如，如果创建了单一数据库，五分钟后删除了它，则将按该数据库存在一小时收费。

## <a name="change-storage-size"></a>更改存储大小

### <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

- 可以使用 1GB 作为增量，将存储预配到最大大小限制。 最小可配置数据存储为 5 GB
- 可通过 [Azure 门户](https://portal.azure.com)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) 为单一数据库增加或减少大小上限，以预配存储。
- SQL 数据库会自动为日志文件额外分配 30% 的存储，并为 TempDB 的每个 vCore 分配 32GB，但不会超过 384GB。 TempDB 位于所有服务层级中的附加 SSD 上。
- 单一数据库的存储价格等于数据存储与日志存储量之和乘以服务层级的存储单价。 vCore 价格已包括 TempDB 费用。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

### <a name="dtu-based-purchasing-model"></a>基于 DTU 的购买模型

- 单一数据库的 DTU 价格附送了一定容量的存储，无需额外费用。 超出附送的量后，可花费额外的费用预配额外的存储，但不能超过存储上限，不超过 1 TB 时，以 250 GB 为增量进行预配，超出 1 TB 时，以 256 GB 为增量进行预配。 有关包括的存储量和大小上限，请参阅[单一数据库：存储大小和计算大小](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)。
- 可通过 Azure 门户、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) 为单一数据库增加大小上限，以预配额外存储。
- 单一数据库的额外存储价格等于额外存储量乘以服务层级的额外存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

### <a name="geo-replicated-database"></a>异地复制的数据库

若要更改复制的辅助数据库的数据库大小，请更改主数据库的大小。 此更改也会在辅助数据库上复制并实现。 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>当最大大小超过 1 TB 时，P11 和 P15 约束

高级层中的存储空间超过 1 TB 目前在除：中国东部、中国北部、德国中部、德国北部、美国中部、US DoD 地区和美国政府中心以外的所有地区都可用。 在这些区域，高级层中的最大存储限制为 1 TB。 对于最大大小超过 1 TB 的 P11 和 P15 数据库，存在以下注意事项和限制：

- 如果 P11 或 P15 数据库的最大大小已设置为大于 1 TB 的值，则只能将其还原或复制到 P11 或 P15 数据库。  随后，如果重新缩放操作时分配的空间量不超过新计算大小的最大大小限制，则可以将数据库重新缩放到不同的计算大小。
- 对于“活动异地复制”方案：
  - 设置异地复制关系：如果主数据库是 P11 或 P15，则辅助数据库也必须为 P11 或 P15，更低的计算大小会被拒绝作为辅助数据库，因为它们不能支持超过 1 TB。
  - 升级异地复制关系中的主数据库：在主数据库上将最大大小更改为超过 1 TB 将触发辅助数据库上的相同更改。 这两个升级都必须成功才能使主数据库上的更改生效。 超过 1 TB 选项的区域限制适用。 如果辅助数据库位于不支持超过 1 TB 的区域，则不会升级主数据库。
- 不支持将导入/导出服务用于加载超过 1 TB 的 P11/P15 数据库。 使用 SqlPackage.exe 可[导入](sql-database-import.md)和[导出](sql-database-export.md)数据。

## <a name="next-steps"></a>后续步骤

有关总体资源限制，请参阅 [SQL 数据库基于 vCore 的资源限制 - 单一数据库](sql-database-vcore-resource-limits-single-databases.md)和 [SQL 数据库基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-single-databases.md)。
