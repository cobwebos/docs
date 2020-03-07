---
title: 通过弹性池管理多个数据库
description: 使用弹性池管理和缩放多个 SQL 数据库（成千上万的）。 可以按一个价格将资源分布到需要的任何位置。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
ms.date: 08/06/2019
ms.openlocfilehash: 3c476393153f6bc1d18d5c163bcd69484583eb15
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388815"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>弹性池有助于管理和缩放多个 Azure SQL 数据库

SQL 数据库弹性池是一种简单且经济高效的解决方案，用于管理和缩放具有不断变化且不可预测的使用需求的多个数据库。 同一弹性池中的所有数据库位于单个 Azure SQL 数据库服务器上，并以固定价格共享固定数量的资源。 Azure SQL 数据库中的弹性池可使 SaaS 开发人员将一组数据库的价格性能优化在规定的预算内，同时为每个数据库提供性能弹性。

## <a name="what-are-sql-elastic-pools"></a>什么是 SQL 弹性池

SaaS 开发人员构建在由多个数据库组成的大规模数据层上的应用程序。 常见的应用程序模式是为每位客户设置单一数据库。 但不同的客户通常具有不同且不可预测的使用模式，并且很难预测每个数据库用户的资源需求。 传统上，有两个选择：

- 基于高峰使用情况和付款情况过度预配资源或
- 采用低配节省成本，但这样会牺牲高峰期的性能和客户满意度。

弹性池通过确保数据库获取需要时所需的性能资源来解决这个问题。 它们提供了一个可预测预算内的简单的资源分配机制。 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅 [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> 弹性池没有按照数据库收取的费用。 对于池存在的每个小时，需要支付最高的 eDTU 或 vCore 费用，无论使用量是多少，也不管池处于活动状态的时间是否小于一小时。

弹性池可让开发人员为由多个数据库共享的池购买资源，以适应单一数据库使用时段不可预测的情况。 可以根据[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)或[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)为池配置资源。 池的资源要求取决于其数据库的聚合使用量。 池可用的资源数量由开发者预算控制。 开发者只需将数据库添加到池，为数据库设置最小和最大资源（最小和最大 DTU 数，或者最小或最大 vCore 数，具体取决于所选的资源模型），然后基于预算设置池的资源。 开发人员可以使用池顺畅地扩大其服务，以渐增的规模从精简的新创公司发展到成熟的企业。

在池中，单独的数据库都被赋予了在固定参数内自动缩放的灵活性。 高负荷下的数据库可能会消耗更多的资源以满足需求。 低负荷下的数据库消耗较少的资源，没有任何负荷的数据库不会消耗任何资源。 设置整个池（而非单个数据库）的资源简化了管理任务。 此外，必须具有该池的可预测预算。 可将更多资源添加现有池而不会造成数据库关闭，除非需要移动数据库以便提供更多计算资源来预留新 eDTU。 同样，随时可以从现有池中删除不再需要的额外资源。 并且可以向池添加或缩减数据库。 如果可以预测到数据库的资源利用率不足，则将其移出。

> [!NOTE]
> 将数据库移入或移出弹性池时，除了在操作结束时删除数据库连接时有短暂的停机时间（大约为几秒）外，几乎没有停机时间。

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>何时应当考虑使用 SQL 数据库弹性池

池很适合具有特定使用模式的大量数据库。 对于给定的数据库，此模式的特征是低平均使用量与相对不频繁的使用高峰。

可以加入池的数据库越多，实现的节省就越大。 根据应用程序使用模式，可能会看到与两个 S3 数据库一样少的节约。

以下各部分将帮助用户了解如何评估特定的数据库集合是否会因使用池而受益。 这些示例使用标准池，但同样的原理也适用于基本和高级池。

### <a name="assessing-database-utilization-patterns"></a>评估数据库使用模式

下图显示了一个数据库示例，该数据库有大量的闲置时间，但也会定期出现活动高峰。 这是适合池的使用模式：

   ![适用于池的单一数据库](./media/sql-database-elastic-pool/one-database.png)

在所示的五分钟时间段内，DB1 高峰最高达到 90 个 DTU，但其整体平均使用量低于五个 DTU。 在单一数据库中运行此工作负荷需要 S3 计算大小，但在低活动期间，这可使大多数资源处于未使用状态。

池可让这些未使用的 DTU 跨多个数据库共享，因此减少了所需的 DTU 和总体成本。

以上一个示例为基础，假设有其他数据库具有与 DB1 类似的使用模式。 在接下来的两个图中，4 个数据库和 20 个数据库的使用量分层放在同一图形中，以演示在使用基于 DTU 的购买模型时数据库使用量在整个时间段上非重叠的性质：

   ![使用模式适用于池的 4 个数据库](./media/sql-database-elastic-pool/four-databases.png)

   ![使用模式适用于池的 20 个数据库](./media/sql-database-elastic-pool/twenty-databases.png)

在上图中，黑线表示跨所有 20 个数据库的聚合 DTU 使用量。 其中表明聚合 DTU 使用量永远不会超过 100 个 DTU，并指出 20 个数据库可以在这段期间共享 100 个 eDTU。 相比于将每个数据库放入单一数据库的 S3 计算大小，这会导致 DTU 减少 20 倍和价格降低 13 倍。

由于以下原因，此示例很理想：

- 每一数据库之间的高峰使用量和平均使用量有相当大的差异。
- 每个数据库的高峰使用量在不同时间点发生。
- eDTU 在多个数据库之间共享。

池的价格取决于池的 eDTU。 尽管池的 eDTU 单位价格比单一数据库的 DTU 的单位价格多 1.5 倍，但**池 eDTU 可由多个数据库共享，所需的 eDTU 总数更少**。 定价方面和 eDTU 共享的这些差异是池可以提供成本节省可能性的基础。

以下与数据库计数和数据库使用率相关的经验法则可帮助确保池提供相比于使用单一数据库计算大小降低的成本。

### <a name="minimum-number-of-databases"></a>数据库的最小数目

如果单一数据库的资源聚合量比池所需的资源多 1.5 倍，那么弹性池更具成本效益。

***基于 DTU 的购买模型示例***<br>
至少需要 2 个 S3 数据库或 15 个 S0 数据库，才能使 100 个 eDTU 池比使用单一数据库计算大小更具成本效益。

### <a name="maximum-number-of-concurrently-peaking-databases"></a>并发高峰数据库的最大数目

通过共享资源，并非池中的所有数据库都能同时达到使用单一数据库可用资源的最大限制。 并发高峰的数据库越少，可以设置的池资源就越低，也就能实现池更大的成本效益。 一般而言，池中不能有 2/3（或 67%）以上的数据库的高峰同时达到其资源限制。

***基于 DTU 的购买模型示例***

为了降低 200 个 eDTU 池内 3 个 S3 数据库的成本，在使用过程中最多可使其中两个数据库同时处于高峰。 否则，如果四个 S3 数据库中超过两个同时高峰，则必须将池缩放为超过 200 个 eDTU。 如果将池重设大小为超过 200 个 eDTU，则需要加入更多的 S3 数据库到池，才能使成本低于单一数据库的计算大小。

请注意，此示例不考虑池中其他数据库的使用率。 如果在任何给定时间点，所有数据库都有一些使用量，则可以同时处于高峰的数据库应少于 2/3（或 67%）。

### <a name="resource-utilization-per-database"></a>每个数据库的资源使用率

数据库的高峰和平均使用率之间的差异为，长时间的低使用率和短时间的高使用率。 这个使用模式非常适合在数据库之间共享资源。 当数据库的高峰使用率比平均使用率大 1.5 倍左右时，应考虑将数据库用作池。

**基于 DTU 的购买模型示例**：高峰为 100 dtu 且平均使用67个 dtu 或更少的 S3 数据库是在池中共享 edtu 的良好候选项。 或者，高峰为 20 个 DTU 且平均使用 13 个或更少 DTU 的 S1 数据库是池的良好候选项。

## <a name="how-do-i-choose-the-correct-pool-size"></a>如何选择正确的池大小

池的最佳大小取决于聚合池中所有数据库所需的资源。 这涉及到决定以下项：

- 池中所有数据库使用的最大资源（最大 DTU 数或最大 vCore 数，具体取决于所选的资源模型）。
- 池中所有数据库使用的最大存储字节。

有关每个资源模型提供的服务层级，请参阅[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)或[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。

在无法使用工具的情况下，以下分步步骤可以帮助你评估池是否比单一数据库更具成本效益：

1. 通过如下方式来估算池所需的 eDTU 或 vCore：

   对于基于 DTU 的购买模型：MAX(<数据库的总数目 X 每一数据库的平均 DTU 使用率>、<br>  
   <并发高峰数据库的数目 X 每一数据库的高峰 DTU 使用率）

   对于基于 vCore 的购买模型：MAX(&lt;数据库的总数目 X 每一数据库的平均 vCore 使用率&gt;,<br>  
   <并发高峰数据库的数目 X 每一数据库的高峰 vCore 使用率)

2. 通过将池内所有的数据库所需的字节数相加来估算池所需要的存储空间。 然后，确定提供此存储量的 eDTU 池的大小。
3. 对于基于 DTU 的购买模型，请取步骤 1 和步骤 2 中 eDTU 估算值中较大的那个。 对于基于 vCore 的购买模型，请取步骤 1 中的 vCore 估算值。
4. 请参阅 [SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/)，找到大于步骤 3 中估算值的最小池大小。
5. 将步骤 5 的池价格与使用单一数据库适当计算大小的价格相比较。

## <a name="using-other-sql-database-features-with-elastic-pools"></a>将其他 SQL 数据库功能与弹性池一起使用

### <a name="elastic-jobs-and-elastic-pools"></a>弹性作业和弹性池

借助池，可以通过在 **[弹性作业](elastic-jobs-overview.md)** 中运行脚本来简化管理任务。 弹性作业可消除与大量数据库有关的大部分麻烦。

有关用于操作多个数据库的其他数据库工具的详细信息，请参阅[使用 Azure SQL 数据库进行扩展](sql-database-elastic-scale-introduction.md)。

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>弹性池中的数据库的业务连续性选项

共用数据库通常支持和可用于单一数据库相同的[业务连续性功能](sql-database-business-continuity.md)。

- **时间点还原**

  时间点还原使用自动的数据库备份将池中的数据库恢复到特定的时间点。 请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)

- **异地还原**

  当数据库因其所在的区域发生事故而不可用时，异地还原会提供默认的恢复选项。 请参阅[还原 Azure SQL 数据库或故障转移到辅助数据库](sql-database-disaster-recovery.md)

- **活动异地复制**

  对于具有异地还原无法提供的更强烈的恢复要求的应用程序，请配置[活动异地复制](sql-database-active-geo-replication.md)或[自动故障转移组](sql-database-auto-failover-group.md)。

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>使用 Azure 门户创建新的 SQL 数据库弹性池

在 Azure 门户中可以通过两种方法创建弹性池。

1. 请参阅[Azure 门户](https://portal.azure.com)，创建弹性池。 搜索并选择 " **AZURE SQL**"。
2. 选择“+添加”以打开“选择 SQL 部署选项”页。 通过选择 "**数据库**" 磁贴上的 "**显示详细**信息"，可以查看有关弹性池的其他信息。
3. 在 "**数据库**" 磁贴上的 "**资源类型**" 下拉列表中选择 "**弹性池**"，然后选择 "**创建**"：

   ![创建弹性池](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. 或者，你可以通过导航到现有的 Azure SQL server 并单击 " **+ 新建池**" 来创建弹性池，以便直接在该服务器中创建池。

> [!NOTE]
> 可以在服务器上创建多个池，但不能将数据库从不同的服务器添加到同一个池中。

该池的服务层级决定了池中弹性数据库的可用功能，以及每个数据库可用的最大资源量。 有关详细信息，请参阅 [DTU 模型](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)中弹性池的资源限制。 有关弹性池的基于 vCore 的资源限制，请参阅[基于 vCore 的资源限制 - 弹性池](sql-database-vcore-resource-limits-elastic-pools.md)。

若要配置池的资源和定价，请单击“配置池”。 然后选择服务层级，将数据库添加到池，并配置池及其数据库的资源限制。

完成配置池后，可以单击“应用”，将池命名，然后单击“确定”以创建池。

## <a name="monitor-an-elastic-pool-and-its-databases"></a>监视弹性池及其数据库

在 Azure 门户中，可以监视弹性池和该池中的数据库的利用率。 还可以对弹性池进行一组更改，并同时提交所有更改。 这些更改包括添加或删除数据库、更改弹性池设置或更改数据库设置。

若要开始监视弹性池，请在门户中找到并打开该弹性池。 首先，您将看到一个屏幕，该屏幕提供弹性池状态的概述。 这包括：

- 显示弹性池资源使用情况的监视图表
- 针对弹性池的最近警报和建议（如果有）

下图显示一个示例弹性池：

![池视图](./media/sql-database-elastic-pool-manage-portal/basic.png)

如需有关池的详细信息，可在此概述中单击任何可用信息。 单击“资源利用率”图表会转到“Azure 监视”视图，在其中可以自定义图表中显示的指标和时间段。 单击任何显示的通知会转到一个边栏选项卡，其中显示了该警报或建议的完整详细信息。

若要监视池中的数据库，可在左侧资源菜单“监视”部分中单击“数据库资源利用率”。

![数据库资源利用率页](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>自定义图表显示

可以编辑图表和指标页以显示其他指标，如 CPU 百分比、数据 IO 百分比和已用日志 IO 百分比。

在“编辑图表”窗体中，可选择固定时间范围，或单击“自定义”选择过去两周内的任何 24 小时时间段，然后选择要监视的资源。

### <a name="to-select-databases-to-monitor"></a>选择要监视的数据库

默认情况下，“数据库资源利用率”边栏选项卡中的图表按 DTU 或 CPU（取决于服务层级）显示排名靠前的 5 个数据库。 可以在图表下面的列表中，通过选中和取消选中左侧的复选框，在此图表中显示或隐藏相应的数据库。

还可以选择在此数据库表中并列查看更多的指标，以获取更完整的数据库性能视图。

有关详细信息，请参阅[在 Azure 门户中创建 SQL 数据库警报](sql-database-insights-alerts-portal.md)。

## <a name="customer-case-studies"></a>客户案例研究

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart 在 Azure SQL 数据库中使用弹性池，以每月快速扩展其业务服务，每个 Azure SQL 数据库1000。

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco 对 Azure SQL 数据库使用弹性池，以便在云中为数千个租户快速预配和缩放服务。

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   Daxko/CSI 使用弹性池和 Azure SQL 数据库来加速其开发周期和增强其客户服务和性能。   

## <a name="next-steps"></a>后续步骤

- 有关定价信息，请参阅[弹性池定价](https://azure.microsoft.com/pricing/details/sql-database/elastic)。
- 若要缩放弹性池，请参阅[缩放弹性池](sql-database-elastic-pool-scale.md)和[缩放弹性池 - 示例代码](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- 有关视频，请参阅[有关 Azure SQL 数据库弹性功能的 Microsoft 虚拟大学视频课程](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅 [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。
- 有关使用弹性池的 SaaS 教程，请参阅 [Wingtip SaaS 应用程序简介](sql-database-wtp-overview.md)。
- 若要了解具有多个数据库的弹性池中的资源管理，请参阅[密集弹性池中的资源管理](sql-database-elastic-pool-resource-management.md)。
