---
title: "什么是弹性池？ 管理多个 SQL 数据库 - Azure | Microsoft 文档"
description: "使用弹性池管理和缩放多个 SQL 数据库（成千上万的）。 可以按一个价格将资源分布到你需要的任何位置。"
keywords: "多个数据库, 数据库资源, 数据库性能"
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 03/06/2017
ms.author: ddove
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 7490fe7261c760f2945d67a7f819091fd69b04f8
ms.lasthandoff: 04/15/2017


---

# <a name="how-elastic-pools-help-you-manage-and-scale-multiple-sql-databases"></a>弹性池如何帮助你管理和缩放多个 SQL 数据库

SQL 数据库弹性池是一种简单且经济高效的解决方案，用于管理和缩放具有不断变化且不可预测的使用需求的多个数据库。 同一弹性池中的所有数据库位于单个 Azure SQL 数据库服务器上，并以固定价格共享固定数量的资源。

可以使用 [Azure 门户](sql-database-elastic-pool-manage-portal.md)、[PowerShell](sql-database-elastic-pool-manage-powershell.md)、[Transact-SQL](sql-database-elastic-pool-manage-tsql.md)、[C#](sql-database-elastic-pool-manage-csharp.md) 和 REST API 创建和管理弹性池。 弹性池资源是按 [eDTU](sql-database-what-is-a-dtu.md) 度量的。


> [!NOTE]
> 弹性池在所有 Azure 区域中均已正式发布 (GA)，但印度西部除外，这些区域当前仅发布了预览版。  将尽快在此区域中正式发布弹性池。
>
>


## <a name="how-do-elastic-pools-help-manage-database-resources"></a>弹性池如何帮助管理数据库资源？

常见的 SaaS 应用程序模式是单租户数据库模型：每个客户（数据库）都有其自己的数据库。 每个客户对内存、IO 和 CPU 具有不可预测的资源要求。 由于需求有高峰和低谷，如何有效地分配资源和节省成本？ 通常有两个选项：(1) 基于高峰使用情况过度设置资源，因此需要支付额外的费用，或者 (2) 为了节省成本而采用低配，但在高峰期间会出现性能下降而导致客户满意度降低。 弹性池通过确保数据库获取其所需的性能资源以及其需要的时机来解决这个问题。 它们提供了一个可预测预算内的简单的资源分配机制。 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅 [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

在 SQL 数据库中，单一数据库处理资源需求的能力的相对度量值以数据库事务单位 (DTU) 表示，而弹性池中数据库的该能力则以弹性数据库事务单位 (eDTU) 表示。 请参阅 [SQL 数据库简介](sql-database-technical-overview.md)，了解有关 DTU 和 eDTU 的详细信息。

以固定价格为池提供了固定数量的 eDTU。 在池中，单独的数据库都被赋予了在固定参数内自动缩放的灵活性。 高负荷下的数据库可能会消耗更多的 eDTU 以满足需求。 低负荷下的数据库消耗较少的 eDTU，没有任何负荷的数据库不会消耗任何 eDTU。 设置整个池（而非单个数据库）的资源简化了管理任务。 此外，必须具有该池的可预测预算。

可将更多 eDTU 添加现有池而不会造成数据库关闭，除非需要移动数据库以便提供更多计算资源来预留新 eDTU。 同样，你随时可以从现有池中删除不再需要的额外 eDTU。

并且可以向池添加或缩减数据库。 如果可以预测到数据库的资源利用率不足，则将其移出。

## <a name="which-databases-go-in-a-pool"></a>在池中有哪些数据库？
![弹性池中共享 eDTU 的 SQL 数据库。][1]

最适合添加到弹性池的数据库通常是有时活动，有时不活动。 在上述示例中，你可以看到单一数据库的活动、4 个数据库的活动，最后是包含 20 个数据库的弹性池的活动。 活动随时间而不同的数据库很适合添加到弹性池，因为它们不是永远都在使用中，而且可以共享 eDTU。 并非所有数据库都符合此模式。 具有更稳定的资源需求的数据库更适合“基本”、“标准”、“高级”和“高级 RS”服务层，这些层的资源是单独分配的。

[弹性池的价格和性能注意事项](sql-database-elastic-pool-guidance.md)。

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>弹性池的 eDTU 和存储限制

下表描述了“基本”、“标准”、“高级”和“高级 RS”弹性池的特征。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

如果使用了弹性池的所有 DTU，那么池中的每个数据库将接收相同数量的资源来处理查询。  SQL 数据库服务通过确保相等的计算时间片，在数据库之间提供资源共享的公平性。 弹性池资源共享公平性是在将每个数据库的 DTU 最小值设为非零值时，对另外为每个数据库保证的任意资源量的补充。

## <a name="elastic-pool-properties"></a>弹性池属性

下表描述了弹性池和入池数据库的限制。

### <a name="limits-for-elastic-pools"></a>弹性池的限制
| 属性 | 说明 |
|:--- |:--- |
| 服务层 |基本、标准、高级或高级 RS。 服务层确定性能范围和可配置的存储限制的范围，以及业务连续性选择。 池中的每个数据库都有与池相同的服务层。 “服务层”也被称为“版本”。 |
| 每个池的 eDTU 数 |池中的数据库可以共享的 eDTU 的数量上限。 池中的数据库同时使用的 eDTU 总量不能超过此限制。 |
| 每个池的最大存储空间 (GB) |池中的数据库可以共享的存储量上限（以 GB 为单位）。 池中的数据库使用的存储总量不能超过此限制。 此限制取决于每个池的 eDTU。 如果超出此限制，所有数据库都将变成只读。 每个池的最大存储是指池中数据文件的最大存储，不包括日志文件使用的空间。 |
| 每个池的数据库的最大数目 |每个池允许的数据库的最大数目。 |
| 每个池的最大并发工作线程数 |池中可用于所有数据库的最大并发工作线程（请求）数。 |
| 每个池的最大并发登录数 |池中所有数据库的最大并发登录数。 |
| 每个池的最大并发会话数 |池中可用于所有数据库的最大会话数。 |
|||

### <a name="limits-for-pooled-databases"></a>入池数据库的限制
| 属性 | 说明 |
|:--- |:--- |
| 每个数据库的最大 eDTU 数 |根据池中其他数据库的 eDTU 使用率，池中任何数据库可以使用的 eDTU 的最大数目。  每个数据库的 eDTU 上限并不是数据库的资源保障。  此设置是应用于池中所有数据库的全局设置。 将每个数据库的最大 eDTU 数设置得足够高，以处理数据库使用高峰情况。 因为池通常会假定数据库存在热使用模式和冷使用模式，在这些模式中并非所有数据库同时处于高峰使用状态，所以预期会存在某种程度的过量使用情况。 例如，假设每个数据库的高峰使用量为 20 个 eDTU，并且池中 100 个数据库仅有 20% 同时处于高峰使用中。  如果将每个数据库的 eDTU 最大值设为 20 个 eDTU，则可以认为超量 5 倍使用该池是合理的，并且将每个池的 eDTU 数设为 400。 |
| 每个数据库的最小 eDTU 数 |池中任何数据库可以保证的 eDTU 最小数目。  此设置是应用于池中所有数据库的全局设置。 每个数据库的最小 eDTU 可能设为 0，这也是默认值。 该属性值可以设置为介于 0 和每个数据库的平均 eDTU 使用量之间的任意值。 池中数据库数目和每个数据库的 eDTU 下限的积不能超过每个池的 eDTU 数。  例如，如果一个池有 20 个数据库，每个数据库的 eDTU 最小值设为 10 个 eDTU，则池的 eDTU 数目必须大于或等于 200 个 eDTU。 |
| 每个数据库的最大存储空间 (GB) |池中一个数据库的最大存储空间。 入池数据库共享池的存储空间，因此数据库存储空间限制为小于池的剩余存储空间和每个数据库的最大存储空间。 每个数据库的最大存储是指数据文件的最大存储，不包括日志文件使用的空间。 |
|||

## <a name="elastic-jobs"></a>弹性作业
借助池，可以通过在**[弹性作业](sql-database-elastic-jobs-overview.md)**中运行脚本来简化管理任务。 弹性作业可消除与大量数据库有关的大部分麻烦。 若要开始使用该作业，请参阅[弹性作业入门](sql-database-elastic-jobs-getting-started.md)。

有关用于操作多个数据库的其他数据库工具的详细信息，请参阅[使用 Azure SQL 数据库进行扩展](sql-database-elastic-scale-introduction.md)。

## <a name="business-continuity-features-for-databases-in-a-pool"></a>池中数据库的业务连续性功能
入池数据库通常支持可用于单一数据库的相同的[业务连续性功能](sql-database-business-continuity.md)。

### <a name="point-in-time-restore"></a>时间点还原
时间点还原使用自动的数据库备份将池中的数据库恢复到特定的时间点。 请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>异地还原
当数据库因其所在的区域发生事故而不可用时，异地还原会提供默认的恢复选项。 请参阅[还原 Azure SQL 数据库或故障转移到辅助数据库](sql-database-disaster-recovery.md)

### <a name="active-geo-replication"></a>活动异地复制
对于具有异地还原无法提供的更强烈的恢复要求的应用程序，请配置[活动异地复制](sql-database-geo-replication-overview.md)。

## <a name="next-steps"></a>后续步骤

* 可以使用 [Azure 门户](sql-database-elastic-pool-manage-portal.md)、[PowerShell](sql-database-elastic-pool-manage-powershell.md)、[Transact-SQL](sql-database-elastic-pool-manage-tsql.md)、[C#](sql-database-elastic-pool-manage-csharp.md) 和 REST API 创建和管理弹性池。
* 有关何时使用弹性池的指导，请参阅[弹性池指导](sql-database-elastic-pool-guidance.md)。
* 有关视频，请参阅[有关 Azure SQL 数据库弹性功能的 Microsoft 虚拟大学视频课程](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)

<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

