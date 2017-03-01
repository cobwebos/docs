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
ms.date: 02/09/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 920143756a0e0b369cf839681f9c4308f77a4af0


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL 数据库选项和性能：了解每个服务层提供的功能
[Azure SQL 数据库](sql-database-technical-overview.md)提供了&3; 个具有多个性能级别的服务层（**基本**、**标准**和**高级**），用于处理不同的工作负荷。 更高的性能级别提供更多的资源，旨在递增式提供更高的吞吐量。 可在不停机的情况下动态更改[服务层和性能级别](sql-database-service-tiers.md)。 基本、标准和高级服务层都提供 99.99% 的运行时间 SLA、灵活的业务连续性选项、安全功能和按小时计费功能。 

可以使用选定[性能级别](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)上的专用资源创建单一数据库。 还可在[弹性池](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)中管理多个数据库，弹性池中的资源在整个数据库中共享。 可用于单一数据库的资源以数据库事务单位 (DTU) 表示，弹性池以弹性 DTU (eDTU) 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU？](sql-database-what-is-a-dtu.md) 

在这两种情况下，服务层包括“**基本**”、“**标准**”和“**高级**”。 

## <a name="choosing-a-service-tier"></a>选择服务层
下表提供了最适用于不同应用程序工作负荷的层的示例。

| 服务层 | 目标工作负荷 |
| :--- | --- |
| **基本** | 最适合小型数据库，通常支持在给定时间执行一个活动操作。 示例包括用于开发或测试的数据库，或不常使用的小型应用程序。 |
| **标准** |云应用程序的首选选项具有低到中等的 IO 性能要求，支持多个并发查询。 示例包括工作组或 Web 应用程序。 |
| **高级** | 专为具有高 IO 性能要求的高事务量设计，支持多个并发用户。 示例包括支持任务关键型应用程序的数据库。 |

首先确定是要运行单一数据库还是运行共享资源的组数据库。 查看[弹性池注意事项](sql-database-elastic-pool-guidance.md)。 若要确定服务层，首先确定需要的最少数据库功能：

* 单个数据库的最大大小（基本数据库最大 2 GB、标准数据库最大 250 GB、高端性能级别的高级数据库 500 GB 到 1 TB）
* 弹性池的最大总存储量（基本为 117 GB，标准为 1200 GB，高级为 750 GB）
* 每个池最多包含的数据库数（基本池 400 个、标准池 400 个、高级池 50 个）
* 数据库备份保留期（基本数据库 7 天、标准数据库 35 天、高级数据库 35 天）

确定了最低服务层后，就可以确定数据库的性能级别（DTU 数）。 通常情况下，可以先使用标准 S2 和 S3 性能级别。 对于具有高 CPU 或 IO 要求的数据库，开始适合使用高级性能级别。 高级版提供更多的 CPU，并且一开始就提供比最高标准性能水平高出 10 倍的 IO。

## <a name="single-database-service-tiers-and-performance-levels"></a>单一数据库服务层和性能级别
对于单一数据库，每个服务层内都有多个性能级别。 你可以灵活选择最能满足你的工作负荷需求的级别。 如果你需要增加或减少工作负荷，可以轻松更改数据库层。 有关详细信息，请参阅 [更改数据库服务层和性能级别](sql-database-service-tiers.md) 。

尽管有多个托管的数据库，你的数据库仍可确保获得一组资源，并且数据库的预期性能特征不受影响。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> 如需本服务层表中所有其他行的详细说明，请参阅 [服务层功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>上下缩放单一数据库

在最初选择服务层和性能级别之后，可根据实际经验动态地上下缩放单一数据库。 

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
> 如需详细步骤，请参阅[使用 Azure 门户管理单一数据库](sql-database-manage-single-databases-portal.md)、[使用 Powershell 管理单一数据库](sql-database-manage-single-databases-powershell.md)或[使用 Transact-SQL 管理单一数据库](sql-database-manage-single-databases-tsql.md)。
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

> [!IMPORTANT]
> 如需详细步骤，请参阅[使用 Azure 门户管理弹性池](sql-database-elastic-pool-manage-portal.md)、[使用 Powershell 管理弹性池](sql-database-elastic-pool-manage-powershell.md)、[使用 Transact-SQL 管理弹性池](sql-database-elastic-pool-manage-tsql.md)或[使用 C# 管理弹性池](sql-database-elastic-pool-manage-csharp.md)。
>

## <a name="next-steps"></a>后续步骤

* 详细了解[弹性池](sql-database-elastic-pool-guidance.md)和[弹性池的价格和性能注意事项](sql-database-elastic-pool-guidance.md)。
* 了解如何[监视、管理弹性池和调整其大小](sql-database-elastic-pool-manage-portal.md)以及如何[监视单一数据库的性能](sql-database-single-database-monitor.md)。
* 你了解了 SQL 数据库层，接下来请使用[免费帐户](https://azure.microsoft.com/pricing/free-trial/)试用一下这些层并了解[如何创建首个 SQL 数据库](sql-database-get-started.md)。
* 对于迁移方案，可使用 [DTU 计算器](http://dtucalculator.azurewebsites.net/)估计所需的 DTU 数。 




<!--HONumber=Feb17_HO3-->


