---
title: 缩放单一数据库资源 - Azure SQL 数据库 | Microsoft Docs
description: 本文介绍如何在 Azure SQL 数据库中缩放适用于单一数据库的计算和存储资源。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: c6dc49204c0a7e1cb0d1116e29746eed2fe52f8d
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286255"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>在 Azure SQL 数据库中缩放单一数据库资源

本文介绍如何在 Azure SQL 数据库中缩放适用于单一数据库的计算和存储资源。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库，但未来的所有开发都不适用于 Az.Sql 模块。 有关这些 cmdlet，请参阅[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 命令在 Az 模块和 AzureRm 模块中的参数是大体上相同的。

## <a name="change-compute-resources-vcores-or-dtus"></a>更改计算资源 （Vcore 或 Dtu）

首先选择 Vcore 或 Dtu 数，你可以缩放单一数据库向上或向下根据实际体验使用动态[Azure 门户](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)， [TRANSACT-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)， [PowerShell](/powershell/module/az.sql/set-azsqldatabase)，则[Azure CLI](/cli/azure/sql/db#az-sql-db-update)，则[REST API](https://docs.microsoft.com/rest/api/sql/databases/update)。

下面的视频演示了如何动态更改服务层和计算大小以增加单一数据库的可用 DTU。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>更改服务层或重新缩放计算实例大小的影响

更改服务层或计算单个数据库的大小主要涉及服务执行以下步骤：

1. 创建新的计算实例的数据库  

    请求的服务层和计算实例大小被创建新的计算实例的数据库。 对于服务层和计算大小更改的某些组合，数据库的副本必须这涉及到将数据复制到新的计算实例中创建，并且可以会对总体延迟。 无论如何，在此步骤中，数据库保持联机状态并连接继续指向原始的计算实例中的数据库。

2. 切换到新的计算实例的连接的路由

    将删除原始的计算实例中的数据库的现有连接。 任何新的连接建立到数据库中新的计算实例。 对于服务层和计算大小更改的某些组合，数据库文件分离再重新附加在切换期间。  无论如何，交换机可能会导致短暂的服务中断时通常为 30 秒内，通常只有几秒钟，数据库将不可用。 如果有长时间运行的运行时将删除连接的事务，此步骤的持续时间可能需要更长时间才能恢复中止的事务数。 [加速数据库恢复](sql-database-accelerated-database-recovery.md)可以减少从中止长时间运行的事务的影响。

> [!IMPORTANT]
> 在工作流中的任何步骤期间不会丢失任何数据。

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>更改服务层或重新缩放计算实例大小的滞后时间

若要更改服务层或重新缩放单一数据库或弹性池的计算大小的延迟是参数化，如下所示：

|服务层|基本单一数据库</br>标准 (S0-S1)|基本弹性池</br>标准 (S2-S12) </br>超大规模， </br>常规用途的单一数据库或弹性池|高级或业务关键的单个数据库或弹性池|
|:---|:---|:---|:---|
|**基本单一数据库</br>标准 (S0-S1)**|&bull; &nbsp;常量时间内延迟独立使用的空间</br>&bull; &nbsp;通常情况下，小于 5 分钟|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|
|**基本弹性池</br>标准 (S2-S12)</br>超大规模，</br>常规用途单一数据库或弹性池**|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|&bull; &nbsp;常量时间内延迟独立使用的空间</br>&bull; &nbsp;通常情况下，小于 5 分钟|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|
|**高级或业务关键的单个数据库或弹性池**|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|

> [!TIP]
> 要监视进行中的操作，请参阅：[使用 SQL REST API 管理操作](https://docs.microsoft.com/rest/api/sql/operations/list)、[使用 CLI 管理操作](/cli/azure/sql/db/op)、[使用 T-SQL 监视操作](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)以及这两个 PowerShell 命令：[Get AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity)并[停止 AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>其他注意事项时更改服务层或重新缩放的计算大小

- 如果要升级到更高的服务层或计算大小，除非显式指定了更大的大小（最大），否则，最大数据库大小不会增大。
- 若要对数据库进行降级，数据库所用空间必须小于目标服务层和计算大小允许的最大大小。
- 从高级层降级至标准层时，如果同时满足 (1) 目标计算大小支持该数据库的最大大小，(2) 最大大小超出目标计算大小包括的存储量，那么将产生额外存储费用。 例如，如果将最大大小为 500 GB 的 P1 数据库缩小至 S3，那么将产生额外的存储费用，因为 S3 支持的最大大小为 500 GB，而它的附送存储量仅为 250 GB。 因此，额外存储量为 500 GB – 250 GB = 250 GB。 有关额外存储定价的信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 如果实际使用的空间量小于附送的存储量，只要将数据库最大大小减少到附送的量，就能避免此项额外费用。
- 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下升级数据库时，请先将其辅助数据库升级到所需的服务层和计算大小，然后再升级主数据库（用于实现最佳性能的常规指南）。 在升级到另一版本时，必须首先升级辅助数据库。
- 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下降级数据库时，请先将其主数据库降级到所需的服务层和计算大小，然后再降级辅助数据库（用于实现最佳性能的常规指南）。 在降级到另一版本时，必须首先降级主数据库。
- 各服务层的还原服务不同。 如果要降级到基本层，则备份保持期也将缩短。 请参阅 [Azure SQL 数据库备份](sql-database-automated-backups.md)。
- 所做的更改完成之前不会应用数据库的新属性。

### <a name="billing-during-rescaling"></a>计费期间重新缩放

将根据使用最高服务层的数据库存在的每个小时 + 在该小时适用的计算大小进行计费，无论使用方式或数据库处于活动状态是否少于一小时。 例如，如果创建了单一数据库，五分钟后删除了它，则将按该数据库存在一小时收费。

## <a name="change-storage-size"></a>更改存储大小

### <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

- 可以使用 1GB 作为增量，将存储预配到最大大小限制。 最小可配置数据存储为 5 GB
- 可通过 [Azure 门户](https://portal.azure.com)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) 为单一数据库增加或减少大小上限，以预配存储。
- SQL 数据库会自动为日志文件额外分配 30% 的存储，并为 TempDB 的每个 vCore 分配 32GB，但不会超过 384GB。 TempDB 位于所有服务层中的附加 SSD 上。
- 单一数据库的存储价格等于数据存储与日志存储量之和乘以服务层的存储单价。 vCore 价格已包括 TempDB 费用。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

### <a name="dtu-based-purchasing-model"></a>基于 DTU 的购买模型

- 单一数据库的 DTU 价格附送了一定容量的存储，无需额外费用。 超出附送的量后，可花费额外的费用预配额外的存储，但不能超过存储上限，不超过 1 TB 时，以 250 GB 为增量进行预配，超出 1 TB 时，以 256 GB 为增量进行预配。 有关包括的存储量和大小上限，请参阅[单一数据库：存储大小和计算大小](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)。
- 可通过 Azure 门户、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) 为单一数据库增加大小上限，以预配额外存储。
- 单一数据库的额外存储价格等于额外存储量乘以服务层的额外存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>基于 DTU 的购买模型：当最大大小超过 1 TB 时，P11 和 P15 的限制

除以下区域外，其他所有区域的高级层目前均可提供超过 1 TB 的存储：中国东部、中国北部、德国中部、德国东北部、美国中西部、US DoD 区域和美国政府中部。 在这些区域，高级层中的最大存储限制为 1 TB。 有关详细信息，请参阅[P11-P15 当前限制](sql-database-single-database-scale.md#dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。 对于最大大小超过 1 TB 的 P11 和 P15 数据库，存在以下注意事项和限制：

- 如果 P11 或 P15 数据库的最大大小曾经设置为一个值大于 1 TB，则它只能还原或复制到 P11 或 P15 数据库。  随后，提供在缩放操作时分配的空间量不超过新的计算大小的最大大小限制为不同的计算大小重新缩放数据库。
- 对于“活动异地复制”方案：
  - 设置异地复制关系：如果主数据库是 P11 或 P15，则辅助数据库也必须为 P11 或 P15，更低的计算大小会被拒绝作为辅助数据库，因为它们不能支持超过 1 TB。
  - 升级异地复制关系中的主数据库：在主数据库上将最大大小更改为超过 1 TB 将触发辅助数据库上的相同更改。 这两个升级都必须成功才能使主数据库上的更改生效。 超过 1 TB 选项的区域限制适用。 如果辅助数据库位于不支持超过 1 TB 的区域，则不会升级主数据库。
- 不支持将导入/导出服务用于加载超过 1 TB 的 P11/P15 数据库。 使用 SqlPackage.exe 可[导入](sql-database-import.md)和[导出](sql-database-export.md)数据。

## <a name="next-steps"></a>后续步骤

有关总体资源限制，请参阅 [SQL 数据库基于 vCore 的资源限制 - 单一数据库](sql-database-vcore-resource-limits-single-databases.md)和 [SQL 数据库基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-single-databases.md)。
