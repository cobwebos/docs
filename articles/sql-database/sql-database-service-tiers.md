---
title: "SQL 数据库性能：服务层 | Microsoft Docs"
description: "比较 SQL 数据库服务层。"
keywords: "数据库选项,数据库性能"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 03/06/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: ab637f9910523cc8d8967dd1507dbcfad9f7ae88
ms.lasthandoff: 03/28/2017


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL 数据库选项和性能：了解每个服务层提供的功能

[Azure SQL 数据库](sql-database-technical-overview.md)提供四个服务层：**基本**、**标准**、**高级**和**高级 RS**。 每个服务层提供多个性能级别来处理不同的工作负荷。 更高的性能级别提供更多的资源，旨在逐级提高吞吐量。 可在不停机的情况下动态更改服务层和性能级别。 基本、标准和高级服务层都提供 99.99% 的运行时间 SLA、灵活的业务连续性选项、安全功能和按小时计费功能。 高级 RS 层提供的性能级别、安全功能和业务连续性功能与高级层相同，但 SLA 更低。

> [!IMPORTANT]
> 与高级或标准数据库相比，高级 RS 数据库以更少的冗余副本运行。 因此，在发生服务故障时，可能需要从备份中恢复数据库，这最长会出现 5 分钟的滞后时间。
>

可以使用服务层中具有特定[性能级别](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)的专用资源创建单一数据库。 还可以在[弹性池](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)中创建数据库，弹性池中的资源在多个数据库之间共享。 可用于单一数据库的资源以数据库事务单位 (DTU) 表示，可用于弹性池的资源则以弹性数据库事务单位 (eDTU) 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU？](sql-database-what-is-a-dtu.md) 

## <a name="choosing-a-service-tier"></a>选择服务层
下表提供了最适用于不同应用程序工作负荷的层的示例。

| 服务层 | 目标工作负荷 |
| :--- | --- |
| **基本** | 最适合小型数据库，通常支持在给定时间执行一个活动操作。 示例包括用于开发或测试的数据库，或不常使用的小型应用程序。 |
| **标准** |云应用程序的首选选项具有低到中等的 IO 性能要求，支持多个并发查询。 示例包括工作组或 Web 应用程序。 |
| **高级** | 专为具有高 IO 性能要求的高事务量设计，支持多个并发用户。 示例包括支持任务关键型应用程序的数据库。 |
| **高级 RS** | 专为不需要最高可用性保证的 IO 密集型工作负荷设计。 示例包括测试高性能工作负荷或数据库不是记录系统的分析工作负荷。 |
|||

首先请确定是要运行包含定义数量的专用资源的单一数据库，还是要在一组数据库之间共享某个资源池。 查看[弹性池注意事项](sql-database-elastic-pool-guidance.md)。 若要确定服务层，首先确定需要的最少数据库功能：

| **服务层功能** | **基本** | **标准** | **高级** | **高级 RS**|
| :-- | --: | --: | --: | --: |
| 单个数据库的最大大小 | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| 弹性池中的最大总存储 | 117 GB | 1200 GB | 750 GB | 750 GB |
| 每个池的数据库数目上限 | 400  | 400 | 50 | 50 |
| 数据库备份保留期 | 7 天 | 35 天 | 35 天 | 35 天 |
||||||

> [!IMPORTANT]
> 使用 P11 和 P15 性能级别的客户最多可以使用 4 TB 的包含存储，而无需额外付费。 此 4 TB 选项目前在以下区域中处于公共预览状态：美国东部 2、美国西部、西欧、东南亚、日本东部、澳大利亚东部、加拿大中部和加拿大东部。 有关当前的限制，请参阅[当前的 4 TB 限制](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

确定了最低服务层后，就可以确定数据库的性能级别（DTU 数）。 通常情况下，可以先使用标准 S2 和 S3 性能级别。 对于具有高 CPU 或 IO 要求的数据库，开始适合使用高级性能级别。 高级版提供更多的 CPU，并且一开始就提供比最高标准性能水平高出 10 倍的 IO。

## <a name="single-database-service-tiers-and-performance-levels"></a>单一数据库服务层和性能级别
对于单一数据库，每个服务层内都有多个性能级别。 可以使用 [Azure 门户](sql-database-manage-single-databases-portal.md)、[PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)、[Transact-SQL](sql-database-manage-single-databases-tsql.md)、C# 和 REST API 灵活选择最适合你的工作负荷的要求的级别。 

尽管有多个托管的数据库，你的数据库仍可确保获得一组资源，并且数据库的预期性能特征不受影响。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> 如需本服务层表中所有其他行的详细说明，请参阅 [服务层功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>上下缩放单一数据库

在最初选择服务层和性能级别之后，可根据实际经验动态地上下缩放单一数据库。 如果你需要向上或向下缩放，可以使用 [Azure 门户](sql-database-manage-single-databases-portal.md)、[PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)、[Transact-SQL](sql-database-manage-single-databases-tsql.md)、C# 和 REST API 轻松更改数据库层。 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

更改数据库的服务层和/或性能级别将在新的性能级别创建原始数据库的副本，然后将连接切换到副本。 当我们切换到副本时，在此过程中不会丢失任何数据，但在短暂的瞬间，将禁用与数据库的连接，因此可能回滚某些处于进行状态的事务。 此窗口会有所不同，但平均起伏少于 4 秒，而在超过 99%的情况下为不足 30 秒。 如果有很大数量的事务在连接禁用的瞬间正在进行中，则此窗口停留时间可能会更长。  

整个扩展过程的持续时间同时取决于更改前后数据库的大小和服务层。 例如，一个正在更改到标准服务层、从标准服务层更改或在标准服务层内更改的 250 GB 的数据库应在 6 小时内完成。 对于与正在高级服务层内更改性能级别的大小相同的数据库，它应在 3 小时内完成。

* 若要对数据库进行降级，数据库应小于目标服务层允许的最大大小。 
* 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下升级数据库时，必须先将辅助数据库升级到所需的性能层，然后再升级主数据库。
* 从高级服务层降级时，必须先终止所有异地复制关系。 可以按照[在中断后恢复](sql-database-disaster-recovery.md)主题中所述的步骤停止主数据库与活动次要数据库之间的复制过程。
* 各服务层提供的还原服务各不相同。 如果进行降级，你可能无法再还原到某个时间点，或者备份保留期变短。 有关详细信息，请参阅 [Azure SQL 数据库备份和还原](sql-database-business-continuity.md)。
* 所做的更改完成之前不会应用数据库的新属性。

> [!IMPORTANT]
> 如需详细步骤，请参阅[在 Azure 门户中管理单一数据库](sql-database-manage-single-databases-portal.md)、[使用 PowerShell 管理单一数据库](scripts/sql-database-monitor-and-scale-database-powershell.md)或[使用 Transact-SQL 管理单一数据库](sql-database-manage-single-databases-tsql.md)。
>

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>弹性池服务层和性能 (eDTU)

池允许数据库共享和使用 eDTU 资源，无需为该池中的每个数据库分配特定性能级别。 例如，标准池中的单一数据库可使用 0 个 eDTU 到最大数据库 eDTU 数（配置池时设置的）运转。 弹性池允许多个具有不同工作负荷的数据库有效地使用在整个池中都可用的 eDTU 资源。 有关详细信息，请参阅 [弹性池的价格和性能注意事项](sql-database-elastic-pool-guidance.md) 。

下表描述了池服务层的特征。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

池中的每个数据库也遵循该层的单一数据库特征。 例如，基本池具有每池最大会话数为 4800 - 28800 的限制，但该基本池中单个数据库具有 300 个会话数的数据库限制。

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>上下缩放弹性池

在最初选择服务层和性能级别之后，可根据实际经验动态地上下缩放弹性池。 

* 更改每个数据库的最小 eDTU 数或每个数据库的最大 eDTU 数通常可在五分钟或更少的时间内完成。
* 更改池大小 (eDTU) 所需的时间取决于池中所有数据库的总大小。 更改平均起来每 100 GB 需要 90 分钟或更短的时间。 例如，如果池中所有数据库的总空间为 200 GB，则更改每个池的池 eDTU 时，预计延迟为 3 小时或更短的时间。

如需详细步骤，请参阅[在 Azure 门户中管理弹性池](sql-database-elastic-pool-manage-portal.md)、[使用 Powershell 管理弹性池](scripts/sql-database-monitor-and-scale-pool-powershell.md)、[使用 Transact-SQL 管理弹性池](sql-database-elastic-pool-manage-tsql.md)或[使用 C# 管理弹性池](sql-database-elastic-pool-manage-csharp.md)。

## <a name="creating-or-upgrading-to-4tb"></a>创建或升级到 4TB

以下部分介绍 4 TB 选项的实现细节。

### <a name="creating-in-the-azure-portal"></a>在 Azure 门户中创建

创建 P11/P15 数据库时，系统已预先选择默认的 1TB 存储选项。 对于位于一个受支持区域中的数据库，可将最大存储空间增加到 4TB。 对于其他所有区域，无法更改存储滑块。 选择 4 TB 的随附存储时，价格不会更改。

### <a name="creating-using-powershell-or-transact-sql"></a>使用 PowerShell 或 Transact-SQL 创建

创建 P11/P15 数据库时，可将最大大小值设置为 1 TB（默认值）或 4 TB。 也接受“1024 GB”和“4096 GB”值。 如果在不受支持的区域中预配数据库，则选择 4 TB 最大大小选项时，create 命令将会失败并出错。

### <a name="upgrading-to-4tb"></a>升级到 4TB 

对于位于一个受支持区域中的 P11 和 P15 数据库，可将最大存储大小增加到 4 TB。 可在 Azure 门户、PowerShell 或 Transact-SQL 中执行此操作。 以下示例演示如何使用 ALTER DATABASE 命令更改最大大小：

```ALTER DATABASE <DatabaseName> MODIFY (MAXSIZE = 4096 GB);
```

Upgrading an existing P11 or P15 database can only be performed by a server-level principal login or by members of the dbmanager database role. 
If executed in a supported region the configuration will be updated immediately. This can be checked using the [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) or by inspecting the database size in the Azure portal. The database will remain online during the upgrade process. However, you will not be able to utilize the full 4 TB of storage until the actual database files have been upgraded to the new maxsize. The length of time required depends upon on the size of the database being upgraded.  

### Error messages
When creating or upgrading an P11/P15 database in an unsupported region, the create or upgrade operation will fail with the following error message: **P11 and P15 database with up to 4TB of storage are available in US East 2, West US, South East Asia, West Europe, Canada East, Canada Central, Japan East, and Australia East.**

## Current limitations of P11 and P15 databases with 4 TB maxsize

- When creating or updating a P11 or P15 database, you can only chose between 1 TB and 4 TB maxsize. Intermediate storage sizes are not currently supported.
- The 4 TB database maxsize cannot be changed to 1 TB even if the actual storage used is below 1 TB. Thus, you cannot downgrade a P11-4TB/P15-4TB to a P11-1TB/P15-1TB or a lower performance tier (e.g., to P1-P6) until we are providing additional storage options for the rest of the performance tiers. This restriction also applies to the restore and copy scenarios including point-in-time, geo-restore, long-term-backup-retention, and database copy. Once a database is configured with the 4 TB option, all restore operations of this database must be into a P11/P15 with 4 TB maxsize.
- For Active Geo-Replication scenarios:
   - Setting up a geo-replication relationship: If the primary database is P11 or P15, the secondary(ies) must also be P11 or P15; lower performance tiers will be rejected as secondaries since they are not capable of supporting 4 TB.
   - Upgrading the primary database in a geo-replication relationship: Changing the maxsize to 4 TB on a primary database will trigger the same change on the secondary database. Both upgrades must be successful for the change on the primary to take effect. Region limitations for the 4TB option apply (see above). If the secondary is in a region that does not support 4 TB, the primary will not be upgraded.
- Using the Import/Export service for loading P11-4TB/P15-4TB databases is not supported. Use SqlPackage.exe to [import](sql-database-import-sqlpackage.md) and [export](sql-database-export-sqlpackage.md) data.

## Next steps

* Learn the details of [elastic pools](sql-database-elastic-pool-guidance.md) and [price and performance considerations for elastic pools](sql-database-elastic-pool-guidance.md).
* Learn how to [Monitor, manage, and resize elastic pools](sql-database-elastic-pool-manage-portal.md) and [Monitor the performance of single databases](sql-database-single-database-monitor.md).
* Now that you know about the SQL Database tiers, try them out with a [free account](https://azure.microsoft.com/pricing/free-trial/) and learn [how to create your first SQL database](sql-database-get-started.md).
* For migration scenarios, use the [DTU Calculator](http://dtucalculator.azurewebsites.net/) to approximate the number of DTUs needed. 


