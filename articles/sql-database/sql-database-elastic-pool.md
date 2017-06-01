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
ms.date: 05/31/2017
ms.author: ddove
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: de0c9969ec28e7b6e57bc384ac3ec47191096a9b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---

# <a name="elastic-pools-help-you-manage-and-scale-multiple-sql-databases"></a>弹性池有助于管理和缩放多个 SQL 数据库

SQL 数据库弹性池是一种简单且经济高效的解决方案，用于管理和缩放具有不断变化且不可预测的使用需求的多个数据库。 同一弹性池中的所有数据库都位于单个 Azure SQL 数据库服务器上，并以固定价格共享固定数量的资源（[弹性数据库事务单位](sql-database-what-is-a-dtu.md) (eDTU)）。 Azure SQL 数据库中的弹性池可使 SaaS 开发人员将一组数据库的价格性能优化在规定的预算内，同时为每个数据库提供性能弹性。   

> [!NOTE]
> 弹性池在所有 Azure 区域中均已正式发布 (GA)，但印度西部除外，这些区域当前仅发布了预览版。  将尽快在此区域中正式发布弹性池。
>

## <a name="overview-of-elastic-pools"></a>弹性池概述 

SaaS 开发人员构建在由多个数据库组成的大规模数据层上的应用程序。 常见的应用程序模式是为每位客户设置单一数据库。 但不同的客户通常拥有不同和不可预测的使用模式，很难预测每位数据库用户的资源需求。 传统上，有两个选择： 

- 基于高峰使用情况和付款情况过度预配资源或
- 采用低配节省成本，但这样会牺牲高峰期的性能和客户满意度。 

弹性池通过确保数据库获取需要时所需的性能资源来解决这个问题。 它们提供了一个可预测预算内的简单的资源分配机制。 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅 [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

弹性池可让开发人员为由多个数据库共享的池购买[弹性数据库事务单位](sql-database-what-is-a-dtu.md) (eDTU)，以适应单一数据库使用时段不可预测的情况。 池的 eDTU 要求取决于其数据库的聚合使用量。 池可用的 eDTU 数量由开发者预算控制。 开发人员只需将数据库添加到池，为数据库设置 eDTU 最小值和最大值，然后根据其预算设置池的 eDTU。 开发人员可以使用池顺畅地扩大其服务，以渐增的规模从精简的新创公司发展到成熟的企业。

在池中，单独的数据库都被赋予了在固定参数内自动缩放的灵活性。 高负荷下的数据库可能会消耗更多的 eDTU 以满足需求。 低负荷下的数据库消耗较少的 eDTU，没有任何负荷的数据库不会消耗任何 eDTU。 设置整个池（而非单个数据库）的资源简化了管理任务。 此外，必须具有该池的可预测预算。 可将更多 eDTU 添加现有池而不会造成数据库关闭，除非需要移动数据库以便提供更多计算资源来预留新 eDTU。 同样，你随时可以从现有池中删除不再需要的额外 eDTU。 并且可以向池添加或缩减数据库。 如果可以预测到数据库的资源利用率不足，则将其移出。

可以使用 [Azure 门户](sql-database-elastic-pool-manage-portal.md)、[PowerShell](sql-database-elastic-pool-manage-powershell.md)、[Transact-SQL](sql-database-elastic-pool-manage-tsql.md)、[C#](sql-database-elastic-pool-manage-csharp.md) 和 REST API 创建和管理弹性池。 

## <a name="when-to-consider-a-pool"></a>何时应考虑使用池
池很适合具有特定使用模式的大量数据库。 对于给定的数据库，此模式的特征是低平均使用量与相对不频繁的使用高峰。

可以加入池的数据库越多，实现的节省就越大。 但根据应用程序使用模式，你可能会看到与使用两个 S3 数据库一样少的节约。  

以下各部分将帮助用户了解如何评估特定的数据库集合是否会因使用池而受益。 这些示例使用标准池，但同样的原理也适用于基本和高级池。

### <a name="assessing-database-utilization-patterns"></a>评估数据库使用模式
下图显示了一个数据库示例，该数据库有大量的闲置时间，但也会定期出现活动高峰。 这是适合池的使用模式：

   ![适用于池的单一数据库](./media/sql-database-elastic-pool/one-database.png)

在所示的五分钟时间段内，DB1 高峰最高达到 90 个 DTU，但其整体平均使用量低于五个 DTU。 在单一数据库中，运行此工作负荷需要 S3 性能级别，但在低活动期间，大多数资源都处在未使用的状态。

池可让这些未使用的 DTU 跨多个数据库共享，因此减少了所需的 DTU 和总体成本。

以上一个示例为基础，假设有其他数据库具有与 DB1 类似的使用模式。 在接下来的两个图形中，4 个数据库和 20 个数据库的使用量分层放在相同的图形，以说明随时间推移，它们的使用率非重叠的性质：

   ![使用模式适用于池的 4 个数据库](./media/sql-database-elastic-pool/four-databases.png)

  ![使用模式适用于池的 20 个数据库](./media/sql-database-elastic-pool/twenty-databases.png)

在上图中，黑线表示跨所有 20 个数据库的聚合 DTU 使用量。 其中表明聚合 DTU 使用量永远不会超过 100 个 DTU，并指出 20 个数据库可以在这段期间共享 100 个 eDTU。 相比于将每个数据库放在单一数据库的 S3 性能级别，这会导致 DTU 减少 20 倍和价格降低 13 倍。

由于以下原因，此示例很理想：

* 每一数据库之间的高峰使用量和平均使用量有相当大的差异。  
* 每个数据库的高峰使用量在不同时间点发生。
* eDTU 在多个数据库之间共享。

池的价格取决于池的 eDTU。 尽管池的 eDTU 单位价格比单一数据库的 DTU 的单位价格多 1.5 倍，但**池 eDTU 可由多个数据库共享，所需的 eDTU 总数更少**。 定价方面和 eDTU 共享的这些差异是池可以提供成本节省可能性的基础。  

以下数据库计数和数据库使用率相关规则的经验法则可帮助确保池提供相比于使用单一数据库的性能级别降低的成本。


### <a name="minimum-number-of-databases"></a>数据库的最小数目
如果单一数据库的性能级别的 DTU 总和比池所需的 eDTU 多 1.5 倍，则弹性池更具成本效益。 有关可用的大小，请参阅[弹性池和弹性数据库的 eDTU 和存储限制](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)。

***示例***<br>
至少需要 2 个 S3 数据库或 15 个 S0 数据库，才能使 100 个 eDTU 池比使用单一数据库性能级别更具成本效益。

### <a name="maximum-number-of-concurrently-peaking-databases"></a>并发高峰数据库的最大数目
通过共享 eDTU，并非池中的所有数据库都能同时使用 eDTU 达到使用单一数据库的性能级别时的最大限制。 并发高峰的数据库越少，可以设置的池 eDTU 就越低，也就能实现池更大的成本效益。 一般而言，池中不能有 2/3（或 67%）以上的数据库的高峰同时达到其 eDTU 限制。

***示例***<br>
为了降低 200 个 eDTU 池内 3 个 S3 数据库的成本，在使用过程中最多可使其中两个数据库同时处于高峰。 否则，如果四个 S3 数据库中超过两个同时高峰，则必须将池缩放为超过 200 个 eDTU。 如果将池缩放大小为超过 200 个 eDTU，则需要加入更多的 S3 数据库到池，使成本始终低于单一数据库的性能级别。

请注意，此示例未考虑池中其他数据库的使用率。 如果在任何给定时间点，所有数据库都有一些使用量，则可以同时处于高峰的数据库应少于 2/3（或 67%）。

### <a name="dtu-utilization-per-database"></a>每个数据库的 DTU 使用率
数据库的高峰和平均使用率之间的差异为，长时间的低使用率和短时间的高使用率。 这个使用模式非常适合在数据库之间共享资源。 当数据库的高峰使用率比平均使用率大 1.5 倍左右时，应考虑将数据库用作池。

***示例***<br>
高峰为 100 个 DTU 且平均使用 67 个或更少 DTU 的 S3 数据库是在池中共享 eDTU 的良好候选项。 或者，高峰为 20 个 DTU 且平均使用 13 个或更少 DTU 的 S1 数据库是池的良好候选项。

## <a name="sizing-an-elastic-pool"></a>调整弹性池的大小
池的最佳大小取决于聚合 eDTU 和池中所有数据库所需的存储资源。 这涉及到决定以下两个数量的较大值：

* 池中所有数据库使用的最大 DTU。
* 池中所有数据库使用的最大存储字节。

有关可用的大小，请参阅[弹性池和弹性数据库的 eDTU 和存储限制](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)。

SQL数据库自动评估现有 SQL 数据库服务器中数据库的历史资源使用率，并在 Azure 门户中推荐适当的池配置。 除推荐外，内置体验还估算服务器上自定义组数据库的 eDTU 使用率。 这使你可以执行“假设”分析，其方法为：通过交互方式将数据库添加到池并删除它们以在提交所做的更改之前获取资源使用率分析和调整建议。 相关操作方式，请参阅[监视、管理弹性池并调整其大小](sql-database-elastic-pool-manage-portal.md)。

在无法使用工具的情况下，以下分步步骤可以帮助你评估池是否比单一数据库更具成本效益：

1. 通过如下方式来估算池所需的 eDTU：

   MAX（<数据库的总数目 X 每一数据库的平均 DTU 使用率>、<br>
   <*并发高峰数据库的数目* X *每一数据库的高峰 DTU 使用率* ）
2. 通过将池内所有的数据库所需的字节数相加来估算池所需要的存储空间。 然后，确定提供此存储量的 eDTU 池的大小。 有关基于 eDTU 池大小的池存储限制，请参阅[弹性池和弹性数据库的 eDTU 和存储限制](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)。
3. 选择步骤 1 和步骤 2 中 eDTU 估算值中较大的那个。
4. 请参阅 [SQL 数据库定价页面](https://azure.microsoft.com/pricing/details/sql-database/)，查找大于步骤 3 中估计值的最低 eDTU 池大小。
5. 将步骤 5 的池价格与单一数据库适当性能级别的价格相比较。

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>弹性池的 eDTU 和存储限制

下表描述了弹性池的资源限制。  请注意，弹性池中各个数据库的资源限制通常与池外部基于 DTU 和服务层的各个数据库相同。  例如，S2 数据库的最大并发辅助进程数为 120 个。  因此，如果池中每个数据库的最大 DTU 是 50 个 DTU（这等效于 S2），则标准池中数据库的最大并发辅助进程数也是 120 个辅助进程。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

如果使用了弹性池的所有 DTU，那么池中的每个数据库将接收相同数量的资源来处理查询。  SQL 数据库服务通过确保相等的计算时间片，在数据库之间提供资源共享的公平性。 弹性池资源共享公平性是在将每个数据库的 DTU 最小值设为非零值时，对另外为每个数据库保证的任意资源量的补充。

## <a name="database-properties-for-pooled-databases"></a>入池数据库的数据库属性

下表介绍了入池数据库的属性。

| 属性 | 说明 |
|:--- |:--- |
| 每个数据库的最大 eDTU 数 |根据池中其他数据库的 eDTU 使用率，池中任何数据库可以使用的 eDTU 的最大数目。  每个数据库的 eDTU 上限并不是数据库的资源保障。  此设置是应用于池中所有数据库的全局设置。 将每个数据库的最大 eDTU 数设置得足够高，以处理数据库使用高峰情况。 因为池通常会假定数据库存在热使用模式和冷使用模式，在这些模式中并非所有数据库同时处于高峰使用状态，所以预期会存在某种程度的过量使用情况。 例如，假设每个数据库的高峰使用量为 20 个 eDTU，并且池中 100 个数据库仅有 20% 同时处于高峰使用中。  如果将每个数据库的 eDTU 最大值设为 20 个 eDTU，则可以认为超量 5 倍使用该池是合理的，并且将每个池的 eDTU 数设为 400。 |
| 每个数据库的最小 eDTU 数 |池中任何数据库可以保证的 eDTU 最小数目。  此设置是应用于池中所有数据库的全局设置。 每个数据库的最小 eDTU 可能设为 0，这也是默认值。 该属性值可以设置为介于 0 和每个数据库的平均 eDTU 使用量之间的任意值。 池中数据库数目和每个数据库的 eDTU 下限的积不能超过每个池的 eDTU 数。  例如，如果一个池有 20 个数据库，每个数据库的 eDTU 最小值设为 10 个 eDTU，则池的 eDTU 数目必须大于或等于 200 个 eDTU。 |
| 每个数据库的最大数据存储 |池中一个数据库的最大存储空间。 入池数据库共享池的存储空间，因此数据库存储空间限制为小于池的剩余存储空间和每个数据库的最大存储空间。 每个数据库的最大存储是指数据文件的最大存储，不包括日志文件使用的空间。 |
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
* 有关视频，请参阅[有关 Azure SQL 数据库弹性功能的 Microsoft 虚拟大学视频课程](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅 [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

