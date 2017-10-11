---
title: "Azure SQL 数据库性能优化指南 |Microsoft 文档"
description: "这篇文章可帮助你确定要为你的应用程序选择哪个服务层。 它还建议来优化应用程序以充分利用 Azure SQL 数据库的方式。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: dd8d95fa-24b2-4233-b3f1-8e8952a7a22b
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/09/2017
ms.author: carlrab
ms.openlocfilehash: dc0244f0e0949b172c391825057f5c14893a5158
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tuning-performance-in-azure-sql-database"></a>Azure SQL 数据库中的优化性能

Azure SQL 数据库提供[建议](sql-database-advisor.md)可用于提高性能的您的数据库，也可以让 Azure SQL 数据库[你的应用程序能够自动适应](sql-database-automatic-tuning.md)并应用更改，以改善你的工作负荷的性能。

在你的没有任何适用的建议，而你仍然有性能问题，你可能会使用以下方法来提高性能：
1. 增加[服务层](sql-database-service-tiers.md)并提供到你的数据库的更多资源。
2. 优化你的应用程序，并应用可以提高性能的一些最佳做法。 
3. 通过更改索引以及更有效地处理数据的查询优化数据库。

这些是手动方法，因为你需要决定什么[服务层](sql-database-service-tiers.md)您可以选择或需要重写应用程序或数据库代码和 deply 所做的更改。

## <a name="increasing-performance-tier-of-your-database"></a>增加数据库性能层

Azure SQL 数据库提供了四个[服务层](sql-database-service-tiers.md)可以从中选择： 基本、 标准、 高级版和高级 RS (数据库吞吐量单位，测量性能或[Dtu](sql-database-what-is-a-dtu.md)。 每个服务层严格隔离你的 SQL 数据库可以使用，并保证该服务级别的可预测性能的资源。 在本文中，我们提供可帮助您选择你的应用程序的服务层的指南。 我们也讨论了你可以调整应用程序以充分利用 Azure SQL 数据库的方式。

> [!NOTE]
> 本文重点介绍在 Azure SQL 数据库中的单个数据库的性能指南。 有关与弹性池相关的性能指南，请参阅[弹性池的价格和性能注意事项](sql-database-elastic-pool-guidance.md)。 但请注意，你可以将优化建议这篇文章中的许多应用到弹性池中的数据库并获取类似的性能好处。
> 

* **基本**： 基本服务层提供良好的性能可预测性每个数据库、 小时的小时。 在基本数据库中，足够的资源支持的小型数据库中不具有多个并发请求良好的性能。 当你将使用基本服务层的典型用例是：
  * **你刚开始使用 Azure SQL Database**。 应用程序中开发通常不需要高性能级别。 基本数据库是一个理想的环境进行数据库开发或测试，较低的价位。
  * **包含单个用户有一个数据库**。 通常将单个用户与数据库关联的应用程序不具有较高的并发能力和性能要求。 这些应用程序是基本服务层进行候选。
* **标准**： 标准服务层提供改进的性能可预测性，并提供良好的性能，具有多个并发请求，例如工作组和 web 应用程序的数据库。 当你选择标准服务层数据库时，你可以调整大小数据库应用程序基于可预测性能，在一分钟。
  * **你的数据库具有多个并发请求**。 通常一次服务多个用户的应用程序需要更高的性能级别。 例如，具有低到中等规模的 IO 流量要求支持多个并发查询的工作组或 web 应用程序非常适合进行标准服务层。
* **高级**： 高级服务层提供可预测的性能，通过第二，为每个高级数据库第二个。 当你选择高级服务层时，你可以根据该数据库的峰值负载数据库应用程序的大小。 该计划中删除在其上的性能变动可导致小型查询耗时超出预期在易受延迟影响操作的情况。 此模型可大大简化需要明确表明最大资源需求、 性能变动或查询延迟的应用程序开发和产品验证环节。 大多数高级服务层使用情况下具有一个或多个这些特征：
  * **高峰值负载**。 需要大量的 CPU、 内存或输入/输出 (I/O)，才能完成其操作的应用程序需要专用、 高性能级别。 例如，已知较长时间使用多个 CPU 内核的数据库操作是高级服务层的候选项。
  * **很多并发请求**。 某些数据库应用程序服务很多并发请求，例如，当为网站具有高流量。 基本和标准服务层限制的每个数据库的并发请求数。 需要更多的连接的应用程序将需要选择相应的预留大小以处理的最大所需请求数。
  * **低延迟时间**。 某些应用程序需要确保在最短时间内从数据库的响应。 如果作为更广泛的客户操作的一部分调用特定的存储的过程，您可能要求不能超过 20 毫秒，99%的时间将从该调用返回。 此类型的应用程序受益于在高级服务层，以确保所需的计算能力可用。
* **高级 RS**: 高级 RS 层适用于不需要最高可用性保证的 IO 密集型工作负荷。 示例包括高性能工作负荷或该数据库未记录的系统的分析工作负荷的测试。

你需要为你的 SQL 数据库的服务级别取决于每个资源维度的峰值负载要求。 某些应用程序使用普通极少量的单个资源，但需要大量的其他资源。

### <a name="service-tier-capabilities-and-limits"></a>服务层功能和限制

在每个服务层，你设置的性能级别，因此你可以灵活地只为所需的容量付费。 你可以[调整容量](sql-database-service-tiers.md)、 向上或向下，根据负载变化。 例如，如果数据库工作负荷在后学校购物季节中较高时，你可以设定的时间，七月至九月增加数据库的性能级别。 你旺季结束时，你可以将其减少。 你可以尽量减少你通过优化你的业务的季节性到云环境所需的付费。 此模型也很适合软件产品发布环节。 包含测试运行，并在它们完成测试，然后释放该容量时，测试团队可能会分配容量。 在容量请求模型中，你为容量付费，因为您需要它，并避免在极少数情况下，你可能使用的专用资源上支出。

### <a name="why-service-tiers"></a>为什么服务层？
尽管每个数据库工作负荷可能有所不同，但服务层的目的是提供在各种性能级别的性能可预测性。 大规模的数据库资源要求的客户可以在更专用的计算环境中工作。

## <a name="tune-your-application"></a>优化你的应用程序
在传统的本地 SQL Server，初始容量规划的过程通常是与分离在生产环境中运行应用程序的过程。 首先，购买的硬件和产品许可证和性能优化此之后完成。 当你使用 Azure SQL 数据库时，它是一个好办法 interweave 运行应用程序和调整它的过程。 与按需容量付款的模型，你可以调整应用程序使用而不是过度配置硬件靠推测的未来增长计划应用程序通常不正确的现在，所需的最小资源。 有些客户可能选择不调整应用程序，并改为选择过度设置硬件资源。 如果不想在关键应用程序的繁忙期更改，此方法可能是一个不错的主意。 但是，优化应用程序可以降至最低资源要求并减少每月费用时在 Azure SQL 数据库中使用的服务层。

### <a name="application-characteristics"></a>应用程序的特征
尽管 Azure SQL 数据库服务层能够提高性能稳定性和可预测性应用程序，但一些最佳做法可以帮助你优化你的应用程序，以更好地利用的资源在性能级别。 虽然许多应用程序只需通过切换到更高的性能级别具有显著的性能提升或服务层，某些应用程序需要附加调谐，才能受益于更高级别的服务。 提高性能，请考虑其他应用程序优化的应用程序具有以下特征：

* **应用程序因"健谈"行为而有性能变慢**。 聊天式应用程序会对网络延迟敏感的过多的数据访问操作。 你可能需要修改这些类型的应用程序以减少到 SQL 数据库的数据访问操作的数量。 例如，您可能通过即席查询分批或将查询移到存储过程等的技术来改进应用程序性能。 有关详细信息，请参阅[批处理查询](#batch-queries)。
* **具有不能支持的一整台计算机的密集型工作负载的数据库**。 超过最高的高级性能级别的资源的数据库可能受益于横向扩展工作负荷。 有关详细信息，请参阅[跨数据库分片](#cross-database-sharding)和[功能分区](#functional-partitioning)。
* **具有非最优查询的应用程序**。 应用程序，尤其是那些中的数据访问层，查询调整不当可能不会受益于更高的性能级别。 这包括缺少 WHERE 子句，缺少索引或统计信息过时的查询。 这些应用程序受益于标准查询性能优化技术。 有关详细信息，请参阅[缺失索引](#identifying-and-adding-missing-indexes)和[查询优化和提示](#query-tuning-and-hinting)。
* **具有并非最佳的数据的应用程序访问设计**。 具有存在固有数据访问并发问题，例如死锁，应用程序可能不会受益于更高的性能级别。 请考虑减少 Azure SQL database 的往返行程数据缓存在客户端与 Azure Caching 服务或其他缓存技术。 请参阅[应用程序层缓存](#application-tier-caching)。

## <a name="tune-your-database"></a>优化数据库
在本部分中，我们了解一些技巧，您可以使用它来优化 Azure SQL 数据库，以获得你的应用程序的最佳性能，并在最低的可能的性能级别运行它。 其中某些技术可与传统的 SQL Server 优化最佳做法，但其他属性特定于 Azure SQL 数据库。 在某些情况下，你可以检查数据库以查找区域以进一步优化和扩展传统的 SQL Server 技术，以便在 Azure SQL 数据库使用的资源。

### <a name="identify-performance-issues-using-azure-portal"></a>确定使用 Azure 门户的性能问题
在 Azure 门户中的以下工具可以帮助你分析和修复性能问题与您的 SQL 数据库：

* [查询性能见解](sql-database-query-performance.md)
* [SQL 数据库顾问](sql-database-advisor.md)

Azure 门户提供了有关这两个这些工具以及如何使用它们的更多信息。 若要有效地诊断并更正问题，我们建议你首次尝试在 Azure 门户中的工具。 我们建议你使用手动优化查找缺少的索引和查询优化，在特殊情况下接下来，我们讨论的方法。

查找有关在标识 Azure SQL Database 中的问题详细信息[性能监视](sql-database-single-database-monitor.md)文章。

### <a name="identifying-and-adding-missing-indexes"></a>确定并添加缺失索引
在 OLTP 数据库性能的一个常见问题与物理数据库设计相关。 通常情况下，数据库架构设计，出厂时无测试规模 （负载或数据卷）。 遗憾的是，查询计划的性能可能在规模可接受，但会大幅降低在生产级数据卷下。 此问题的最常见原因是缺少合适的索引以满足筛选器或在查询中的其他限制。 通常情况下，为表的缺少索引清单扫描时索引搜寻即可满足要求。

在此示例中，所选的查询计划使用扫描搜寻即可满足要求：

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![具有缺失索引的查询计划](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL 数据库可以帮助你查找并修复常见缺少索引情况。 内置于 Azure SQL 数据库的 Dmv 查看索引将大幅降低运行查询的估计的成本的查询编译。 查询在执行期间，SQL 数据库跟踪每个查询计划执行，并跟踪的差距正在执行的查询计划与想象的一个之间存在该索引的频率。 可以使用以下 Dmv 可迅速推测出哪些物理数据库设计的更改可能会提高数据库和及其真实工作负荷的总工作负荷成本。

此查询可用于评估可能缺少的索引：

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

在此示例中，查询会导致此建议：

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

它创建后，该同一 SELECT 语句将选取一个不同的计划，也不能使用搜寻而非扫描，然后更有效地执行该计划：

![具有已更正的索引的查询计划](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

要点是共享商用系统 I/O 容量更多限制比专用的服务器计算机。 最大程度减少不必要的 I/O，以最大限度利用系统中的每个 Azure SQL 数据库服务层的性能级别的 DTU 位于高级。 选择可以显著提高的单个查询延迟、 提高并发处理的请求的单个伸缩单位，吞吐量以及满足查询所需的成本降到最低的相应物理数据库设计。 有关缺少的索引 Dmv 的详细信息，请参阅[sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx)。

### <a name="query-tuning-and-hinting"></a>查询优化和提示
查询优化器在 Azure SQL 数据库中的是类似于传统的 SQL Server 查询优化器。 优化查询和了解推理的最佳做法的大多数查询优化器的模型限制也适用于 Azure SQL 数据库。 如果优化 Azure SQL 数据库中的查询，你可能会降低总资源需求的另一个好处。 你的应用程序可能能够在较低的成本低于从未优化的同等运行，因为它可以在较低的性能级别运行。

这是常见的 SQL Server 和这也适用于 Azure SQL 数据库的一个示例是如何查询优化器"探查"参数。 在编译期间，查询优化器计算的参数以确定它是否可以生成更优的查询计划的当前值。 尽管此策略通常会导致查询计划大大快于不已知的参数值的情况下编译的计划，当前它不能准确同时作用在 SQL Server 和 Azure SQL 数据库中。 有时不探查参数，并有时探查参数，但生成的计划对于工作负荷中的参数值的整套并非最佳。 Microsoft 加入查询提示 （指令），以便你可以更谨慎地指定意图和重写参数探查的默认行为。 通常情况下，如果使用提示，则可以解决在其中的默认 SQL Server 或 Azure SQL 数据库行为是针对特定客户工作负荷不完善的情况。

下一个示例演示如何查询处理器可以生成用于性能和资源要求并非最佳的计划。 此示例还显示，是否使用查询提示时，你可以减少查询运行时间和资源需求，你的 SQL 数据库：

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

安装程序代码将创建具有偏斜数据分布的表。 最佳查询计划不同根据选择的参数。 遗憾的是，计划缓存行为不会始终重新编译查询的最常见的参数值。 因此，很可能并非最佳计划缓存，用于多个值，即使在不同的计划可能是更好的计划选择平均值。 然后查询计划，创建两个完全相同，只不过其中一个具有特殊的查询提示的存储的过程。

**示例中，第 1 部分**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**示例中，第 2 部分**

（我们建议你等待至少 10 分钟，然后再开始的示例中，第 2 部分，以便在所得的遥测数据的不同结果。）

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

此示例中的每个部分尝试运行参数化的 insert 语句 1,000 次 （以产生足够多的负载要用作测试数据集）。 当它执行存储的过程时，查询处理器会检查其 （参数"探查"） 的首次编译期间传递给过程的参数值。 处理器缓存所得的计划，即使参数值不相同，则会使用它进行以后的调用。 在所有情况下可能无法使用最佳计划。 有时，你需要引导优化器选取更适合普通情况而不是特定用例从首次编译查询计划。 在此示例中，初始计划生成读取所有行以查找每个值的参数相匹配的"扫描"计划：

![查询通过使用扫描计划优化](./media/sql-database-performance-guidance/query_tuning_1.png)

由于我们使用值 1 执行该过程，因此所得的计划是最适合的值 1，但是对于表中的所有其他值并非最佳。 结果可能不是您希望如果敧随机选取每个计划，因为计划执行得更慢，并使用更多资源。

如果运行与测试`SET STATISTICS IO`设置为`ON`，在此示例中的逻辑扫描工作完成后台进行的活动。 你可以看到有计划 （这是效率低下，但如果普通情况仅返回一行） 完成了 1148 读取：

![查询通过使用逻辑扫描优化](./media/sql-database-performance-guidance/query_tuning_2.png)

该示例的第二部分使用查询提示告知优化器在编译过程中使用特定值。 在这种情况下，它会强制查询处理器忽略作为参数传递的值，而不是假定`UNKNOWN`。 这是指 （忽略偏斜） 表中具有平均频率的值。 所得的计划是一个基于搜寻的计划，较快，使用较少的资源，平均而言，比计划在此示例的第 1 部分：

![通过使用查询提示的查询优化](./media/sql-database-performance-guidance/query_tuning_3.png)

你可以查看在影响**sys.resource_stats**表 （没有从执行测试，并当数据填充表的时间延迟）。 此示例、 第 1 部分期间 22:25:00 时间窗口中，执行和第 2 部分执行在 22:35:00。 较早的时间窗口 （由于计划效率提高） 更高版本的比该时间窗口中使用更多资源。

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![查询优化示例结果](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> 尽管在此示例中的卷是有意小，但非最佳参数的效果很大，尤其是在更大的数据库上。 差异，在极端情况下，可能是秒对于快速情况和慢速情况下的小时数之间。
> 
> 

你可以检查**sys.resource_stats**以确定测试的资源使用的另一个测试的更多或更少的资源。 当比较数据时时，以便它们不在同一 5 分钟窗口中单独的测试计时**sys.resource_stats**视图。 练习的目标是尽量减小的使用，资源总量，而不是峰值资源降至最低。 通常情况下，优化一段产生延迟的代码还可减少资源消耗。 请确保对应用程序做出的更改是必需的并且这些更改不会的人员可能在应用程序中使用查询提示的客户体验产生负面影响。

如果工作负荷具有一套重复查询，通常最好捕获并验证你计划选择的最优性，原因在于它决定托管数据库所需的最小资源大小单位。 对其进行验证后，有时重新检查计划，以帮助你确保其未降级。 你可以详细了解[查询提示 (TRANSACT-SQL)](https://msdn.microsoft.com/library/ms181714.aspx)。

### <a name="cross-database-sharding"></a>跨数据库分片
由于 Azure SQL 数据库运行在商用硬件上，单个数据库的容量限制将低于传统的本地 SQL Server 安装。 某些客户使用分片技术分散在多个数据库的数据库操作，当操作不适合在 Azure SQL 数据库中的单个数据库的限制内。 大多数客户都在 Azure SQL 数据库中使用分片技术的单个维度上的数据拆分到多个数据库。 有关此方法，你需要了解 OLTP 应用程序通常执行应用于只有一行或小的一组架构中的行的事务。

> [!NOTE]
> SQL 数据库现在提供一个库，以帮助进行分片。 有关详细信息，请参阅[弹性数据库客户端库概述](sql-database-elastic-database-client-library.md)。
> 
> 

例如，如果数据库有客户名称、 订单和订单详细信息 （如附带 SQL Server 的传统示例 Northwind 数据库），则无法通过分组具有相关的订单和订单详细信息的客户将此数据拆分为多个数据库中。 你可以保证客户的数据保留在单个数据库。 应用程序将不同的客户拆分跨数据库，有效地将负载分散到多个数据库。 与分片中，客户不仅可以避免的最大数据库大小限制，但 Azure SQL 数据库还可以处理具有明显大于不同性能级别的限制，只要每个数据库适合其 DTU 的工作负载。

尽管数据库分片不会减少解决方案的聚合资源容量，是高度有效地支持分散在多个数据库的超大型解决方案。 每个数据库可以在不同的性能级别以支持非常大，资源要求高的"有效"数据库运行。

### <a name="functional-partitioning"></a>功能分区
SQL Server 用户经常将单个数据库中的许多功能。 例如，如果应用程序包含管理商店库存的逻辑，该数据库可能具有与库存、 跟踪采购订单、 存储的过程和管理结束月报告的索引或具体化视图关联的逻辑。 此技术，更便于管理数据库操作，如备份，但它还要求你调整硬件以处理应用程序的所有功能的峰值负载的大小。

如果你使用 Azure SQL 数据库中的向外扩展体系结构，它是到不同的数据库应用程序的不同功能拆分一个好办法。 通过使用此技术，每个应用程序独立地缩放。 在应用程序变得更加繁忙 （和数据库上的负载增加时），管理员可以选择应用程序中的每个函数的独立的性能级别。 在的限制，此体系结构，与应用程序可以是大于单个商用计算机可以处理，因为负载分布在多台计算机。

### <a name="batch-queries"></a>批查询
对于通过使用大容量访问数据的应用程序的频繁，即席查询，很长的响应时间是应用程序层和 Azure SQL 数据库层之间的网络通信上花费。 即使应用程序和 Azure SQL 数据库位于同一数据中心中，这两者之间的网络延迟可能会增大大量的数据访问操作。 若要减少网络往返遍历的数据访问操作，请考虑使用选项，要么批处理即席查询，或将其编译为存储的过程。 当进行批量即席查询时，可以将多个查询作为一个大批次在一次行程中发送到 Azure SQL 数据库。 如果在存储过程的即席查询编译时，无法获得相同的结果，就像你执行批处理它们。 使用存储的过程还为你提供好处，即增加将查询计划缓存在 Azure SQL 数据库以便您可以再次使用存储的过程的机会。

某些应用程序被大量写入操作。 有时，你可以通过考虑如何将写入分批减少数据库上的总 I/O 负载。 通常情况下，这是使用显式事务，而不是存储的过程和即席批处理中的自动提交事务一样简单。 你可以使用的不同技术的评估，请参阅[批处理中 Azure SQL Database 应用程序的技术](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx)。 使用你自己的工作负荷，以找到正确的模型的分批进行试验。 请务必了解模型可能略有不同的事务一致性保证。 查找资源用量降至最低的正确工作负荷要求找到一致性与性能权衡的正确组合。

### <a name="application-tier-caching"></a>应用程序层缓存
某些数据库应用程序具有大量读取工作负荷。 缓存层可能会降低数据库上的负载，可能可能会减少支持通过使用 Azure SQL 数据库的数据库所需的性能级别。 与[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)，如果你有大量读取工作负荷，你可以一次读取的数据 (也可以一次每个应用层计算机，根据配置方式)，然后将存储你的 SQL 数据库之外的该数据。 这是一种方法，以减少数据库负载 （CPU 和读取 I/O），但没有事务一致性影响因为从缓存中读取的数据可能会与数据库中的数据不同步。 尽管在许多应用程序是可接受某种程度上的不一致，但这并不适用于所有工作负荷。 在实现应用程序层缓存策略之前，你应完全了解任何应用程序的要求。

## <a name="next-steps"></a>后续步骤
* 有关服务层的详细信息，请参阅[SQL 数据库选项和性能](sql-database-service-tiers.md)
* 有关弹性池的详细信息，请参阅[Azure 的弹性池是什么？](sql-database-elastic-pool.md)
* 有关性能和弹性池的信息，请参阅[时要考虑弹性池](sql-database-elastic-pool-guidance.md)

