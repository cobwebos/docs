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
ms.date: 04/30/2020
ms.openlocfilehash: a13b860e01e7ef295df629d79dfa44700b5f0d02
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691586"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>在 Azure SQL 数据库中缩放单一数据库资源

本文介绍如何在预配的计算层级中缩放适用于 Azure SQL 数据库的计算和存储资源。 另外，[无服务器计算层级](sql-database-serverless.md)提供自动缩放计算功能，并且按秒对使用的计算计费。

最初选择 vCore 或 DTU 数量后，可以使用 [Azure 门户](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、 [PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)，根据实际体验动态扩展或缩减单一数据库。

下面的视频演示了如何动态更改服务层级和计算大小以增加单一数据库的可用 DTU。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="impact"></a>影响

更改服务层级或计算大小主要涉及到由服务执行的以下步骤：

1. 为数据库创建新的计算实例  

    使用请求的服务层级和计算大小创建新的计算实例。 更改后，对于服务层级和计算大小的某些组合，必须在新的计算实例中创建数据库的副本，此过程涉及到数据复制，可能会对总体延迟造成很大的影响。 无论如何，在执行此步骤期间，数据库会保持联机，并且连接会继续定向到原始计算实例中的数据库。

2. 将连接路由切换到新的计算实例

    将删除与原始计算实例中的数据库建立的现有连接。 将与新计算实例中的数据库建立任何新的连接。 更改后，对于服务层级和计算大小的某些组合，在切换期间会分离再重新附加数据库文件。  无论如何，切换操作都可能会导致服务出现短暂的中断，此时，数据库一般会出现 30 秒以下的不可用情况（通常只有几秒钟）。 如果连接断开时有长时间运行的事务正在运行，则此步骤的持续时间可能会变长，以便恢复中止的事务。 [加速的数据库恢复](sql-database-accelerated-database-recovery.md)可以降低中止长期运行的事务的影响。

> [!IMPORTANT]
> 执行工作流中的任何步骤期间都不会丢失数据。 确保已在特定的应用程序和组件中实现某些[重试逻辑](sql-database-connectivity-issues.md)，这些应用程序和组件在服务层级更改时使用 Azure SQL 数据库。

## <a name="latency"></a>延迟 

更改服务层、缩放单个数据库或弹性池的计算大小、将数据库移入/移出弹性池或在弹性池之间移动数据库的估计延迟参数如下：

|服务层|基本单一数据库，</br>标准 (S0-S1)|基本弹性池，</br>标准 (S2-S12)， </br>超大规模， </br>常规用途单一数据库或弹性池|高级或业务关键型单一数据库或弹性池|
|:---|:---|:---|:---|
|**基本单一数据库，</br>标准 (S0-S1)**|&bull;&nbsp;独立于所用空间的固定时间延迟</br>&bull;&nbsp;通常不到5分钟|&bull;&nbsp;由于数据复制所使用的数据库空间的延迟比例</br>&bull;&nbsp;通常，每 GB 的可用空间小于1分钟|&bull;&nbsp;由于数据复制所使用的数据库空间的延迟比例</br>&bull;&nbsp;通常，每 GB 的可用空间小于1分钟|
|**基本弹性池、</br>标准 (S2-S12)、</br>超大规模、</br>常规用途单一数据库或弹性池**|&bull;&nbsp;由于数据复制所使用的数据库空间的延迟比例</br>&bull;&nbsp;通常，每 GB 的可用空间小于1分钟|&bull;&nbsp;独立于所用空间的固定时间延迟</br>&bull;&nbsp;通常不到5分钟|&bull;&nbsp;由于数据复制所使用的数据库空间的延迟比例</br>&bull;&nbsp;通常，每 GB 的可用空间小于1分钟|
|**高级或业务关键型单一数据库或弹性池**|&bull;&nbsp;由于数据复制所使用的数据库空间的延迟比例</br>&bull;&nbsp;通常，每 GB 的可用空间小于1分钟|&bull;&nbsp;由于数据复制所使用的数据库空间的延迟比例</br>&bull;&nbsp;通常，每 GB 的可用空间小于1分钟|&bull;&nbsp;由于数据复制所使用的数据库空间的延迟比例</br>&bull;&nbsp;通常，每 GB 的可用空间小于1分钟|

> [!NOTE]
> 此外，对于标准（S2-S12）和常规用途数据库，如果数据库使用的是高级文件共享（[PFS](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)）存储，则将数据库移入/移出弹性池或在弹性池之间移动数据库的延迟将与数据库大小成正比。
>
> 若要确定数据库是否正在使用 PFS 存储，请在数据库的上下文中执行以下查询。 如果 AccountType 列中的值为`PremiumFileStorage`，则数据库使用的是 PFS 存储。
 
```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> 若要监视正在进行的操作，请参阅：[使用 SQL REST API 管理操作](https://docs.microsoft.com/rest/api/sql/operations/list)、[使用 CLI 管理操作](/cli/azure/sql/db/op)、[使用 t-sql 监视操作](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)和以下两个 PowerShell 命令： [AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity)和[AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

## <a name="cancelling-changes"></a>取消更改

服务层级更改或计算重新缩放操作可以取消。

#### <a name="azure-portal"></a>Azure 门户

在数据库概述边栏选项卡中，导航到“通知”**** 并单击指示有正在进行的操作的磁贴：

![正在进行的操作](media/sql-database-single-database-scale/ongoing-operations.png)

接下来，单击标签为“取消此操作”**** 的按钮。

![取消正在进行的操作](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

从 PowerShell 命令提示符下，设置 `$resourceGroupName`、`$serverName` 和 `$databaseName`，然后运行以下命令：

```azurecli
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
- 从“高级”层级降级至“标准”层级时，如果同时满足 (1) 目标计算大小支持该数据库的最大大小，(2) 最大大小超出目标计算大小包括的存储量，那么将产生额外存储费用********。 例如，如果将最大大小为 500 GB 的 P1 数据库缩小至 S3，那么将产生额外的存储费用，因为 S3 支持的最大大小为 1 TB，而它的附送存储量仅为 250 GB。 因此，额外存储量为 500 GB – 250 GB = 250 GB。 有关额外存储定价的信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 如果实际使用的空间量小于附送的存储量，只要将数据库最大大小减少到附送的量，就能避免此项额外费用。
- 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下升级数据库时，请先将其辅助数据库升级到所需的服务层级和计算大小，然后再升级主数据库（用于实现最佳性能的常规指南）。 升级到其他版本时，需要首先升级辅助数据库。
- 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下降级数据库时，请先将其主数据库降级到所需的服务层级和计算大小，然后再降级辅助数据库（用于实现最佳性能的常规指南）。 当降级到不同版本时，需要先降级主数据库。
- 各服务层级的还原服务不同。 如果要降级到基本层，则备份保持期也将缩短****。 请参阅 [Azure SQL 数据库备份](sql-database-automated-backups.md)。
- 所做的更改完成之前不会应用数据库的新属性。

## <a name="billing"></a>计费 

将根据使用最高服务层级的数据库存在的每个小时 + 在该小时适用的计算大小进行计费，无论使用方式或数据库处于活动状态是否少于一小时。 例如，如果创建了单一数据库，五分钟后删除了它，则将按该数据库存在一小时收费。

## <a name="change-storage-size"></a>更改存储大小

### <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

- 可以将存储设置为数据存储最大大小限制（以 1 GB 为增量）。 可配置的最小数据存储为 1 GB。 有关每个服务目标中的数据存储最大大小限制，请参阅[单一数据库](sql-database-vcore-resource-limits-single-databases.md)和[弹性池](sql-database-vcore-resource-limits-elastic-pools.md)的资源限制文档页。
- 可以通过使用[Azure 门户](https://portal.azure.com)、 [transact-sql](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、 [PowerShell](/powershell/module/az.sql/set-azsqldatabase)、 [Azure CLI](/cli/azure/sql/db#az-sql-db-update)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/update)增加或减少其最大大小，来设置单个数据库的数据存储。 如果以字节为单位指定最大大小值，则该值必须是 1 GB （1073741824字节）。
- 可以存储在数据库的数据文件中的数据量受配置的数据存储最大大小的限制。 除了该存储，SQL 数据库还会自动分配30% 的存储空间用于事务日志。
- 对于`tempdb`数据库，SQL 数据库会自动为每个 vCore 分配 32 GB。 `tempdb`位于所有服务层中的本地 SSD 存储上。
- 单个数据库或弹性池的存储价格是指将数据存储和事务日志存储量乘以服务层的存储单价。 的成本`tempdb`包括在价格中。 有关存储价格的详细信息，请参阅[SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

### <a name="dtu-based-purchasing-model"></a>基于 DTU 的购买模型

- 单一数据库的 DTU 价格附送了一定容量的存储，无需额外费用。 超出附送的量后，可花费额外的费用预配额外的存储，但不能超过存储上限，不超过 1 TB 时，以 250 GB 为增量进行预配，超出 1 TB 时，以 256 GB 为增量进行预配。 有关包括的存储量和大小上限，请参阅[单一数据库：存储大小和计算大小](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)。
- 可以通过使用 Azure 门户、 [transact-sql](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、 [PowerShell](/powershell/module/az.sql/set-azsqldatabase)、 [Azure CLI](/cli/azure/sql/db#az-sql-db-update)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/update)增加其最大大小，来预配单个数据库的额外存储。
- 单一数据库的额外存储价格等于额外存储量乘以服务层级的额外存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

### <a name="geo-replicated-database"></a>异地复制的数据库

若要更改复制的辅助数据库的数据库大小，请更改主数据库的大小。 然后，此更改也会在辅助数据库上复制并实现。 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>当最大大小超过 1 TB 时，P11 和 P15 约束

高级层中的存储空间超过 1 TB 目前在除：中国东部、中国北部、德国中部、德国北部、美国中部、US DoD 地区和美国政府中心以外的所有地区都可用。 在这些区域，高级层中的最大存储限制为 1 TB。 对于最大大小超过 1 TB 的 P11 和 P15 数据库，存在以下注意事项和限制：

- 如果 P11 或 P15 数据库的最大大小已设置为大于 1 TB 的值，则只能将其还原或复制到 P11 或 P15 数据库。  随后，如果重新缩放操作时分配的空间量不超过新计算大小的最大大小限制，则可以将数据库重新缩放到不同的计算大小。
- 对于“活动异地复制”方案：
  - 设置异地复制关系：如果主数据库是 P11 或 P15，则辅助数据库也必须为 P11 或 P15，更低的计算大小会被拒绝作为辅助数据库，因为它们不能支持超过 1 TB。
  - 升级异地复制关系中的主数据库：在主数据库上将最大大小更改为超过 1 TB 将触发辅助数据库上的相同更改。 这两个升级都必须成功才能使主数据库上的更改生效。 超过 1 TB 选项的区域限制适用。 如果辅助数据库位于不支持超过 1 TB 的区域，则不会升级主数据库。
- 不支持将导入/导出服务用于加载超过 1 TB 的 P11/P15 数据库。 使用 SqlPackage.exe 可[导入](sql-database-import.md)和[导出](sql-database-export.md)数据。

## <a name="next-steps"></a>后续步骤

有关总体资源限制，请参阅 [SQL 数据库基于 vCore 的资源限制 - 单一数据库](sql-database-vcore-resource-limits-single-databases.md)和 [SQL 数据库基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-single-databases.md)。
