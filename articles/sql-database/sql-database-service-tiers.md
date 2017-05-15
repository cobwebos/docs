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
ms.custom: resources
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 04/26/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 0ab804ee1dc25f1e44be856564ac8ffa87c54dea
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017


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

首先请确定是要运行包含定义数量的专用资源的单一数据库，还是要在一组数据库之间共享某个资源池。 查看[弹性池注意事项](sql-database-elastic-pool.md)。 若要确定服务层，首先确定需要的最少数据库功能：

| **服务层功能** | **基本** | **标准** | **高级** | **高级 RS**|
| :-- | --: | --: | --: | --: |
| 最大单一数据库大小 | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| 弹性池中的最大数据库大小 | 156 GB | 2.9 TB | 500 GB | 500 GB |
| 每个池的数据库数目上限 | 500  | 500 | 100 | 100 |
| 数据库备份保留期 | 7 天 | 35 天 | 35 天 | 35 天 |
||||||

> [!IMPORTANT]
> 这些额外的存储选项目前在以下区域可用：美国东部 2、美国西部、西欧、东南亚、日本东部、澳大利亚东部、加拿大中部和加拿大东部。 请参阅[当前的 4 TB 限制](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

确定了最低服务层后，就可以确定数据库的性能级别（DTU 数）。 通常情况下，可以先使用标准 S2 和 S3 性能级别。 对于具有高 CPU 或 IO 要求的数据库，开始适合使用高级性能级别。 高级版提供更多的 CPU，并且一开始就提供比最高标准性能水平高出 10 倍的 IO。

## <a name="single-database-service-tiers-and-performance-levels"></a>单一数据库服务层和性能级别
对于单一数据库，每个服务层内都有多个性能级别。 可以使用 Azure 门户、[PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database)、C# 和 REST API 灵活选择最适合工作负荷需求的级别。  

尽管有多个托管的数据库，你的数据库仍可确保获得一组资源，并且数据库的预期性能特征不受影响。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> 如需本服务层表中所有其他行的详细说明，请参阅 [服务层功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>上下缩放单一数据库

在最初选择服务层和性能级别之后，可根据实际经验动态地上下缩放单一数据库。 若需向上或向下缩放，可以使用 Azure 门户、[PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database)、C# 和 REST API 轻松更改数据库层。 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

更改数据库的服务层和/或性能级别将在新的性能级别创建原始数据库的副本，然后将连接切换到副本。 当我们切换到副本时，在此过程中不会丢失任何数据，但在短暂的瞬间，将禁用与数据库的连接，因此可能回滚某些处于进行状态的事务。 此窗口会有所不同，但平均起伏少于 4 秒，而在超过 99%的情况下为不足 30 秒。 如果有很大数量的事务在连接禁用的瞬间正在进行中，则此窗口停留时间可能会更长。  

整个扩展过程的持续时间同时取决于更改前后数据库的大小和服务层。 例如，一个正在更改到标准服务层、从标准服务层更改或在标准服务层内更改的 250 GB 的数据库应在 6 小时内完成。 对于与正在高级服务层内更改性能级别的大小相同的数据库，它应在 3 小时内完成。

* 若要对数据库进行降级，数据库应小于目标服务层允许的最大大小。 
* 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下升级数据库时，必须先将辅助数据库升级到所需的性能层，然后再升级主数据库。
* 从高级服务层降级时，必须先终止所有异地复制关系。 可以按照[在中断后恢复](sql-database-disaster-recovery.md)主题中所述的步骤停止主数据库与活动次要数据库之间的复制过程。
* 各服务层提供的还原服务各不相同。 如果进行降级，你可能无法再还原到某个时间点，或者备份保留期变短。 有关详细信息，请参阅 [Azure SQL 数据库备份和还原](sql-database-business-continuity.md)。
* 所做的更改完成之前不会应用数据库的新属性。

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>弹性池服务层和性能 (eDTU)

池允许数据库共享和使用 eDTU 资源，无需为该池中的每个数据库分配特定性能级别。 例如，标准池中的单一数据库可使用 0 个 eDTU 到最大数据库 eDTU 数（配置池时设置的）运转。 弹性池允许多个具有不同工作负荷的数据库有效地使用在整个池中都可用的 eDTU 资源。 有关详细信息，请参阅 [弹性池的价格和性能注意事项](sql-database-elastic-pool.md) 。

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

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

升级现有 P11 或 P15 数据库只能由服务器级主体登录名或 dbmanager 数据库角色的成员执行。 如果在支持的区域中执行，配置将立即更新。 可以使用 [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) 或通过在 Azure 门户中查看数据库大小来检查此项内容。 在升级过程中，数据库将保持联机。 但是，在实际的数据库文件已升级到新的最大大小之前，你将无法利用完整的 4 TB 存储。 所需的时间长度取决于要升级的数据库的大小。  

### <a name="error-messages"></a>错误消息
在不支持的区域中创建或升级 P11/P15 数据库时，创建或升级操作将会失败，并出现以下错误消息：**最多带有 4 TB 存储的 P11 和 P15 数据库在美国东部 2、美国西部、东南亚、欧洲西部、加拿大东部、加拿大中部、日本东部和澳大利亚东部可用。**

## <a name="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize"></a>最大大小为 4 TB 的 P11 和 P15 数据库的当前限制

- 创建或更新 P11 或 P15 数据库时，只能在 1 TB 和 4 TB 最大大小之间选择。 中间存储大小当前不受支持。
- 即使使用的实际存储低于 1 TB，也不能将 4 TB 的数据库最大大小更改为 1 TB。 因此，不能将 P11-4TB/P15-4TB 降级到 P11-1TB/P15-1TB 或更低的性能层（例如，到 P1-P6），除非我们为其余性能层提供了更多存储选项。 此限制也适用于还原和复制方案，包括时间点、异地还原、长期备份保留以及数据库复制。 数据库配置了 4 TB 选项后，此数据库的所有还原操作都必须适合最大大小为 4 TB 的 P11/P15。
- 对于“活动异地复制”方案：
   - 设置异地复制关系：如果主数据库是 P11 或 P15，则辅助数据库也必须为 P11 或 P15，更低的性能层将被拒绝作为辅助数据库，因为它们不能支持 4 TB。
   - 升级异地复制关系中的主数据库：在主数据库上将最大大小更改到 4 TB 将触发辅助数据库上的相同更改。 这两个升级都必须成功才能使主数据库上的更改生效。 4TB 选项的区域限制适用（请参阅上文）。 如果辅助数据库位于不支持 4 TB 的区域，则不会升级主数据库。
- 将导入/导出服务用于加载 P11-4TB/P15-4TB 数据库不受支持。 使用 SqlPackage.exe 可[导入](sql-database-import.md)和[导出](sql-database-export.md)数据。

## <a name="next-steps"></a>后续步骤

* 详细了解[弹性池](sql-database-elastic-pool.md)和[弹性池的价格和性能注意事项](sql-database-elastic-pool.md)。
* 了解如何[监视、管理弹性池和调整其大小](sql-database-elastic-pool-manage-portal.md)以及如何[监视单一数据库的性能](sql-database-single-database-monitor.md)。
* 你了解了 SQL 数据库层，接下来请使用[免费帐户](https://azure.microsoft.com/pricing/free-trial/)试用一下这些层并了解[如何创建首个 SQL 数据库](sql-database-get-started-portal.md)。
* 对于迁移方案，可使用 [DTU 计算器](http://dtucalculator.azurewebsites.net/)估计所需的 DTU 数。 


