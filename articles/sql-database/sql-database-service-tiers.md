---
title: "SQL 数据库性能：服务层 | Microsoft Docs"
description: "比较 SQL 数据库服务层。"
keywords: "数据库选项,数据库性能"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/15/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 7bbdbe345bd468c01e2a790610bcf6c063c11f9b


---
# <a name="sql-database-service-tiers-for-single-databases-and-elastic-database-pools"></a>单一数据库和弹性数据库池的 SQL 数据库服务层
[Azure SQL 数据库](sql-database-technical-overview.md) 提供了三个具有多个性能级别的服务层，用来处理不同的工作负荷。 更高的性能级别提供更多的资源集，旨在递增式提供更高的吞吐量。 可以动态更改服务层和性能级别。 有关详细信息，请参阅 [更改数据库服务层和性能级别](sql-database-scale-up.md) 。

可以在其自身自带性能级别的 [服务层](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) 中管理每个数据库。 还可以在具有共享资源集的[弹性数据库池](sql-database-service-tiers.md#elastic-database-pool-service-tiers-and-performance-in-edtus)中管理多个数据库。 可用于单一数据库的资源以数据库事务单位 (DTU) 表示，可用于弹性数据库池的资源则以弹性 DTU 或 eDTU 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU](sql-database-what-is-a-dtu.md)。 

在这两种情况下，服务层包括“**基本**”、“**标准**”和“**高级**”。 

## <a name="service-tiers"></a>服务层
基本、标准和高级服务层都提供 99.99% 的运行时间 SLA 和可预测的性能、灵活的业务连续性选项、安全功能和按小时计费。 下表提供了最适用于不同应用程序工作负荷的层的示例。

| 服务层 | 目标工作负荷 |
| :--- | --- |
| **基本** |最适合小型数据库，通常支持在给定时间执行一个活动操作。 示例包括用于开发或测试的数据库，或不常使用的小型应用程序。 |
| **标准** |大多数云应用程序的首选选项，支持多个并发查询。 示例包括工作组或 Web 应用程序。 |
| **高级** |专为高事务量设计，支持大量并发用户，并且需要最高级别的业务连续性功能。 示例包括支持任务关键型应用程序的数据库。 |

> [!NOTE]
> Web Edition 和 Business Edition 已停用。 如果你打算继续使用 Web 和 Business Edition，请参阅 [版本停用常见问题](https://azure.microsoft.com/pricing/details/sql-database/web-business/) 。
> 
> 

## <a name="single-database-service-tiers-and-performance-levels"></a>单一数据库服务层和性能级别
对于单一数据库，每个服务层内都有多个性能级别。 你可以灵活选择最能满足你的工作负荷需求的级别。 如果需要向上或向下缩放，可以快速更改数据库的性能级别。 有关详细信息，请参阅 [更改数据库服务层和性能级别](sql-database-scale-up.md) 。

此处列出的性能特征适用于使用 [SQL 数据库 V12](sql-database-technical-overview.md)创建的数据库。 尽管有多个托管的数据库，你的数据库仍可确保获得一组资源，并且数据库的预期性能特征不受影响。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> 如需本服务层表中所有其他行的详细说明，请参阅 [服务层功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。
> 
> 

## <a name="elastic-database-pool-service-tiers-and-performance-in-edtus"></a>弹性数据库池服务层和性能 (eDTU)
也可以在[弹性数据库池](sql-database-elastic-pool.md)中管理多个数据库。 弹性数据库池中的所有数据库共享一组公用资源。 性能特征由 *弹性数据库事务单位* (eDTU) 数度量。 与单一数据库一样，池也有三个服务层：**基本**、**标准**和**高级**。 对于池，这三个服务层仍定义整体性能限制和多个功能。

弹性池允许数据库共享和使用 DTU 资源，而无需为该池中的每个数据库分配特定性能级别。 例如，标准池中的数据库可使用 0 个 eDTU 到最大数据库 eDTU 数（配置池时设置的）运转。 弹性池允许多个具有不同工作负荷的数据库有效地使用在整个池中都可用的 eDTU 资源。 有关详细信息，请参阅[弹性数据库池的价格和性能注意事项](sql-database-elastic-pool-guidance.md)。

下表描述了基本、标准和高级弹性数据库池的特征。

[!INCLUDE [SQL DB service tiers table for elastic database pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

池中的每个数据库也遵循该层的单一数据库特征。 例如，基本池具有每池最大会话数为 4800 - 28800 的限制，但该基本池中单个数据库具有 300 个会话数的数据库限制。

## <a name="choosing-a-service-tier"></a>选择服务层
要确定服务层，需要先确定数据库应当是单一数据库，还是应当是弹性数据库池的一部分。 

### <a name="choosing-a-service-tier-for-a-single-database"></a>为单一数据库选择服务层
要为单一数据库确定服务层，需要先确定所需的数据库功能以选择 SQL 数据库版本：

* 数据库大小（基本数据库最大 2 GB、标准数据库最大 250 GB、高级数据库 500 GB 到 1 TB - 具体取决于性能级别）
* 数据库备份保留期（基本数据库 7 天、标准数据库 35 天、高级数据库 35 天）

确定了 SQL 数据库版本后，就可以确定数据库的性能级别（DTU 数）。 可以根据实际经验猜测，然后 [动态地增加或减少](sql-database-scale-up.md) 。 还可以使用 [DTU 计算器](http://dtucalculator.azurewebsites.net/) 估计所需的 DTU 数。 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>选择弹性数据库池的服务层。
要决定弹性数据库池的服务层，先确定所需的数据库功能，以选择池的服务层。

* 数据库大小（基本数据库 2GB、标准数据库 250 GB 和高级数据库 500 GB）
* 数据库备份保留期（基本数据库 7 天、标准数据库 35 天、高级数据库 35 天）
* 每个池的数据库数（基本池 400 个、标准池 400 个、高级池 50 个）
* 每个池的最大存储（基本池 117 GB、标准池 1200、高级池 750）

确定了池的服务层后，就可以确定池的性能级别 (eDTU)。 可以根据实际经验猜测，然后 [动态地增加或减少](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) 。 可以使用 [DTU 计算器](http://dtucalculator.azurewebsites.net/) 估计池中每个数据库所需的 DTU 数，以帮助确定该池的上限。

## <a name="next-steps"></a>后续步骤
* [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)中详细介绍了这些层的定价。
* 了解有关[弹性数据库池](sql-database-elastic-pool-guidance.md)和[弹性数据库池的价格和性能注意事项](sql-database-elastic-pool-guidance.md)的详细信息。
* 了解如何[监视、管理弹性数据库池和调整其大小](sql-database-elastic-pool-manage-portal.md)以及如何[监视单一数据库的性能](sql-database-single-database-monitor.md)。
* 你了解了 SQL 数据库层，接下来请使用[免费帐户](https://azure.microsoft.com/pricing/free-trial/)试用一下这些层并了解[如何创建首个 SQL 数据库](sql-database-get-started.md)。

## <a name="additional-resources"></a>其他资源
* [使用 Azure SQL 数据库的多租户 SaaS 应用程序设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Microsoft 虚拟大学中介绍 Azure SQL 数据库内弹性数据库功能的视频课程](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)




<!--HONumber=Nov16_HO4-->


