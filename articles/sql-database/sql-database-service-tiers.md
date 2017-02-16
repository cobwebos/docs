---
title: "SQL 数据库性能：服务层 | Microsoft Docs"
description: "比较 SQL 数据库服务层。"
keywords: "数据库选项,数据库性能"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/11/2017
ms.author: carlrab; janeng
translationtype: Human Translation
ms.sourcegitcommit: 0a00aff343bfd31c956f6cbc831e89cc1cc84b23
ms.openlocfilehash: 95ae4bd67b7d08755035e7b5559ca9648d45bdaa


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL 数据库选项和性能：了解每个服务层提供的功能
[Azure SQL 数据库](sql-database-technical-overview.md)提供了 3 个具有多个性能级别的服务层（**基本**、**标准**和**高级**），用于处理不同的工作负荷。 更高的性能级别提供更多的资源，旨在递增式提供更高的吞吐量。 可在不停机的情况下动态更改[服务层和性能级别](sql-database-scale-up.md)。 基本、标准和高级服务层都提供 99.99% 的运行时间 SLA、灵活的业务连续性选项、安全功能和按小时计费功能。 

可以使用选定[性能级别](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)上的专用资源创建单一数据库。 还可在[弹性池](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)中管理多个数据库，弹性池中的资源在整个数据库中共享。 可用于单一数据库的资源以数据库事务单位 (DTU) 表示，弹性池以弹性 DTU (eDTU) 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU？](sql-database-what-is-a-dtu.md)。 

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

确定了最低服务层后，就可以确定数据库的性能级别（DTU 数）。 在许多情况下，标准 S2 和 S3 性能级别是很好的起点。 对于具有高 CPU 或 IO 要求的数据库，开始适合使用高级性能级别。 高级版提供更多的 CPU，并且一开始就提供比最高标准性能水平高出 10 倍的 IO。

在最初选择性能级别之后，可根据实际经验动态地调整[单个数据库](sql-database-scale-up.md)或[弹性池](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool)的大小。 对于迁移方案，还可使用 [DTU 计算器](http://dtucalculator.azurewebsites.net/)估计所需的 DTU 数。 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

## <a name="single-database-service-tiers-and-performance-levels"></a>单一数据库服务层和性能级别
对于单一数据库，每个服务层内都有多个性能级别。 你可以灵活选择最能满足你的工作负荷需求的级别。 如果你需要增加或减少工作负荷，可以轻松更改数据库层。 有关详细信息，请参阅 [更改数据库服务层和性能级别](sql-database-scale-up.md) 。

尽管有多个托管的数据库，你的数据库仍可确保获得一组资源，并且数据库的预期性能特征不受影响。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> 如需本服务层表中所有其他行的详细说明，请参阅 [服务层功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。
> 

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>弹性池服务层和性能 (eDTU)

池允许数据库共享和使用 eDTU 资源，无需为该池中的每个数据库分配特定性能级别。 例如，标准池中的单一数据库可使用 0 个 eDTU 到最大数据库 eDTU 数（配置池时设置的）运转。 弹性池允许多个具有不同工作负荷的数据库有效地使用在整个池中都可用的 eDTU 资源。 有关详细信息，请参阅 [弹性池的价格和性能注意事项](sql-database-elastic-pool-guidance.md) 。

下表描述了池服务层的特征。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

池中的每个数据库也遵循该层的单一数据库特征。 例如，基本池具有每池最大会话数为 4800 - 28800 的限制，但该基本池中单个数据库具有 300 个会话数的数据库限制。


## <a name="next-steps"></a>后续步骤

* 详细了解[弹性池](sql-database-elastic-pool-guidance.md)和[弹性池的价格和性能注意事项](sql-database-elastic-pool-guidance.md)。
* 了解如何[监视、管理弹性池和调整其大小](sql-database-elastic-pool-manage-portal.md)以及如何[监视单一数据库的性能](sql-database-single-database-monitor.md)。
* 你了解了 SQL 数据库层，接下来请使用[免费帐户](https://azure.microsoft.com/pricing/free-trial/)试用一下这些层并了解[如何创建首个 SQL 数据库](sql-database-get-started.md)。




<!--HONumber=Jan17_HO2-->


