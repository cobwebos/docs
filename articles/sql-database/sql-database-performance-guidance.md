---
title: "Azure SQL 数据库性能优化指南 | Microsoft Docs"
description: "此文可帮助你确定哪个服务层适合应用程序。 它还会提供调整建议使的应用程序可以充分利用 Azure SQL 数据库。"
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
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tuning-performance-in-azure-sql-database"></a>优化 Azure SQL 数据库性能

借助 Azure SQL 数据库提供的[建议](sql-database-advisor.md)，可以提升数据库性能，也可以让 Azure SQL 数据库[自动适应应用程序](sql-database-automatic-tuning.md)并应用更改来提升工作负载性能。

如果没有任何适用的建议，但仍有性能问题，可以使用下列方法来提升性能：
1. 增加[服务层](sql-database-service-tiers.md)并向数据库提供更多资源。
2. 优化应用程序，并应用一些可以提升性能的最佳做法。 
3. 通过更改索引和查询来优化数据库，以便更有效地处理数据。

这些都是手动方法，因为需要决定选择或需要哪些[服务层](sql-database-service-tiers.md)，以便重写应用程序或数据库代码，并部署所做的更改。

## <a name="increasing-performance-tier-of-your-database"></a>增加数据库性能层

Azure SQL 数据库提供以下四个[服务层](sql-database-service-tiers.md)可供选择：基本、标准、高级和高级 RS（通过数据库吞吐量单位 ([DTU](sql-database-what-is-a-dtu.md)) 衡量性能）。 每个服务层可严格隔离 SQL 数据库可以使用的资源，并保证相应服务级别的可预测性能。 在本文中，我们将提供指南，帮助你选择应用程序的服务层。 另外，还会讨论如何优化应用程序以充分利用 Azure SQL 数据库。

> [!NOTE]
> 本文侧重于 Azure SQL 数据库中单一数据库的性能指南。 有关弹性池的性能指南，请参阅[弹性池的价格和性能注意事项](sql-database-elastic-pool-guidance.md)。 不过，请注意，也可以将本文中的多项优化建议应用于弹性池中的数据库，获得类似的性能优势。
> 

* **基本**：基本服务层可以准确预测各个数据库的每小时性能。 在基本数据库中，足够的资源可支持不具有多个并发请求的小型数据库中的良好性能。 需要使用基本服务层的典型用例为：
  * **刚开始使用 Azure SQL 数据库**。 开发中的应用程序通常不需要高性能级别。 基本数据库是用于数据库开发或测试的理想环境，价位较低。
  * **数据库包含单个用户**。 将单个用户与某个数据库进行关联的应用程序通常在并发能力和性能方面不会有很高的要求。 这些应用程序是基本服务层的候选。
* **标准**：标准服务层改进了性能预测，并能提升包含多个并发请求的数据库（如工作组和 Web 应用程序）的性能。 选择标准服务层数据库时，可以根据每分钟的可预测性能调整数据库应用程序的大小。
  * **数据库具有多个并发请求**。 同时为多个用户服务的应用程序通常需要更高的性能级别。 例如，具有低到中等规模 IO 流量要求且支持多个并发查询的工作组或 Web 应用程序是标准服务层的最佳选择。
* **高级**：高级服务层可预测各个高级数据库的每秒性能。 选择高级服务层时，可以根据该数据库的峰值负载调整数据库应用程序的大小。 该计划消除了在易受延迟影响操作中性能变动可导致小型查询耗时超出预期的情况。 对于需要明确表明最大资源需求、性能变动或查询延迟的应用程序，此模型可大大简化开发和产品验证周期。 大多数高级服务层用例都具有一个或多个下列特征：
  * **高峰值负载**。 需要大量 CPU、内存或输入/输出 (I/O) 才能完成其操作的应用程序，都需要专用、高性能级别。 例如，已知较长时间使用多个 CPU 内核的数据库操作非常适合高级服务层。
  * **多个并发请求**。 某些数据库应用程序为多个并发请求服务，例如，为具有高流量的网站服务。 基本服务层和标准服务层对每个数据库的并发请求数都有限制。 需要更多连接的应用程序将需要选择合适的预留大小，以处理最大所需的请求数。
  * **低延迟时间**。 某些应用程序需要确保在最短时间内从数据库获得响应。 如果在更大范围的客户操作期间调用了特定存储过程，则有 99% 的时间可能要求在 20 毫秒内从该调用返回。 此类型的应用程序受益于高级服务层，以确保提供所需的计算能力。
* **高级 RS**：高级 RS 层专为不需要最高可用性保证的 IO 密集型工作负载设计。 示例包括测试高性能工作负荷或数据库不是记录系统的分析工作负荷。

SQL 数据库所需的服务级别取决于每个资源维度的峰值负载要求。 某些应用程序可能少量使用某个资源，但需要大量使用其他资源。

### <a name="service-tier-capabilities-and-limits"></a>服务层功能和限制

在每个服务层，由你设置性能级别，因此可以灵活地只为所需的容量付费。 可以根据工作负荷变化向上或向下[调整容量](sql-database-service-tiers.md)。 例如，如果数据库工作负荷在返校购物季期间很高，则可在设定的时间内（七月到九月）提高数据库的性能级别， 而在高峰季结束之后降低性能级别。 可以按业务的季节性因素优化云环境，将支出降至最低。 此模型也很适合软件产品发布环节。 测试团队可在进行测试运行时分配容量，一旦完成测试，即释放该容量。 在容量请求模型中，只为所需容量付费，而避免在很少使用的专用资源上支出。

### <a name="why-service-tiers"></a>为什么使用服务层？
尽管每个数据库工作负荷可能各不相同，但服务层的目的就是在不同的性能级别下提供性能可预测性。 数据库资源要求繁杂的客户可以在更专用的计算环境中运行其数据库。

## <a name="tune-your-application"></a>优化应用程序
在传统的本地 SQL Server 中，初始容量规划的过程通常与在生产环境中运行应用程序的过程分离。 首先，购买硬件和产品许可证，然后完成性能优化。 当使用 Azure SQL 数据库时，最好混杂运行和调整应用程序的过程。 使用按需支付容量的模型，可以优化应用程序以使用目前所需的最少资源，而不是靠推测应用程序的未来增长计划过度配置硬件（这通常是不正确的做法）。 有些客户可能选择不优化应用程序，而是选择过度配置硬件资源。 如果不想在繁忙期更改关键应用程序，这个方法可能是一个不错的主意。 但是，在使用 Azure SQL 数据库中的服务层时，优化应用程序可以使资源需求降至最低并减少每月的费用。

### <a name="application-characteristics"></a>应用程序特征
尽管 Azure SQL 数据库服务层旨在提高应用程序的性能稳定性和可预测性，但一些最佳做法可以帮助你优化应用程序，以便更好地利用某一性能级别中的资源。 虽然许多应用程序只需通过切换到更高的性能级别或服务层便会显著提升性能，但某些应用程序需要进一步优化，才能受益于更高级别的服务。 为了提高性能，请考虑对具有以下特征的应用程序进行进一步的应用程序优化：

* **因“闲聊”行为而性能变慢的应用程序**。 闲聊应用程序会进行过多的对网络延迟敏感的数据访问操作。 可能需要修改这些类型的应用程序，以减少对 SQL 数据库进行的数据访问操作的数量。 例如，可以通过批处理即席查询或将查询移到存储过程等技术来提高应用程序性能。 有关详细信息，请参阅[批处理查询](#batch-queries)。
* **具有不受整台计算机支持的密集型工作负荷的数据库**。 超过最高高级性能级别的资源的数据库可能受益于横向扩展工作负荷。 有关详细信息，请参阅[跨数据库分片](#cross-database-sharding)和[功能分区](#functional-partitioning)。
* **具有非最优查询的应用程序**。 没有很好优化查询的应用程序（尤其是数据访问层中的那些应用程序），则可能不会受益于更高的性能级别。 其中包括缺少 WHERE 子句、缺少索引或统计信息过时的查询。 标准查询性能优化技术能够为这些应用程序带来好处。 有关详细信息，请参阅[缺少索引](#identifying-and-adding-missing-indexes)和[查询优化和提示](#query-tuning-and-hinting)。
* **具有非最优数据访问设计的应用程序**。 选择较高的性能级别可能无法为存在固有数据访问并发问题（例如死锁）的应用程序带来好处。 应考虑通过使用 Azure 缓存服务或其他缓存技术将数据缓存在客户端，减少与 Azure SQL 数据库之间的往返次数。 请参阅[应用程序层缓存](#application-tier-caching)。

## <a name="tune-your-database"></a>优化数据库
在本节中，我们将了解一些用于优化 Azure SQL 数据库的技术，以获取应用程序的最佳性能，并以尽可能低的性能级别运行。 其中某些技术可与传统 SQL Server 优化最佳做法搭配使用，但其他技术是特定于 Azure SQL 数据库的。 在某些情况下，可以检查数据库的使用资源找到进一步优化的区域，并扩展传统 SQL Server 技术以便在 Azure SQL 数据库中使用。

### <a name="identify-performance-issues-using-azure-portal"></a>使用 Azure 门户发现性能问题
Azure 门户中的以下工具可帮助分析和解决 SQL 数据库的性能问题：

* [查询性能见解](sql-database-query-performance.md)
* [SQL 数据库顾问](sql-database-advisor.md)

Azure 门户提供了有关这两个工具以及使用方法的更多信息。 若要有效地诊断并更正问题，建议首先在 Azure 门户中尝试这两个工具。 建议使用接下来要讨论的手动优化方法，用于特殊案例中的缺少索引和查询优化。

若要详细了解如何在 Azure SQL 数据库中发现问题，请参阅[性能监视](sql-database-single-database-monitor.md)一文。

### <a name="identifying-and-adding-missing-indexes"></a>发现并添加缺失索引
OLTP 数据库性能有一个常见问题与物理数据库设计有关。 设计和交付数据库架构时，通常不进行规模（负载或数据卷）测试。 遗憾的是，在规模较小时，查询计划的性能可能尚可接受，但在生产级数据卷下性能就会大幅降低。 此问题最常见的原因是缺乏相应的索引，无法满足筛选器或查询中的其他限制。 缺少索引经常导致表扫描，而此时索引搜寻即可满足要求。

在此示例中，所选的查询计划在搜寻即可满足要求时使用扫描：

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

![缺少索引的查询计划](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL 数据库有助于查找并修复常见缺少索引情况。 Azure SQL 数据库内置的 DMV 将查找其中索引会大幅降低运行查询的估算成本的查询编译。 在查询执行期间，SQL 数据库跟踪每个查询计划的执行频率，并跟踪执行查询计划与想象其中存在该索引的查询计划之间的差距。 可以使用这些 DMV 迅速推测出哪些物理数据库设计更改可能减少数据库的总工作负荷成本及其真实工作负荷。

可以使用此查询评估可能缺少的索引：

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

创建它后，该 SELECT 语句将选取一个不同的计划（该计划使用搜寻而非扫描），然后更高效地执行该计划：

![已更正索引的查询计划](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

其中的要点是共享商用系统与专用的服务器计算机相比，I/O 容量比较有限。 客观上鼓励将多余 I/O 降至最低，最大限度地在 Azure SQL 数据库服务层的每个性能级别 DTU 范围内利用系统。 选择适当的物理数据库设计方式可显著缩短单个查询的延迟、提高对于每个缩放单位可处理的并发请求的吞吐量，以及将满足查询所需的成本降至最低。 有关缺少索引 DMV 的详细信息，请参阅 [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx)。

### <a name="query-tuning-and-hinting"></a>查询优化和提示
Azure SQL 数据库中的查询优化器与传统的 SQL Server 查询优化器相似。 有关优化查询和了解查询优化器的推理模型限制的最佳实践也大多适用于 Azure SQL 数据库。 如果优化 Azure SQL 数据库中的查询，则可能会获得另一个降低总资源需求的好处。 与未经优化的同等应用程序相比，应用程序可能能够以更低的成本运行，因为它可用更低的性能级别运行。

SQL Server 中常见的、也适用于 Azure SQL 数据库的一个示例是，查询优化器如何“探查”参数。 在编译期间，查询优化器将计算参数的当前值，以确定它是否可以生成更优的查询计划。 尽管此策略生成的查询计划通常明显快于未用已知参数值编译的计划，但当前它在 SQL Server 和 Azure SQL 数据库中都不能正常工作。 有时不探查参数，有时探查参数，但对于工作负荷中的整套参数值而言，生成的计划并非最佳。 Microsoft 设计了查询提示（指令），使用户可以更谨慎地指定意图并取代参数探查的默认行为。 一般而言，如果使用提示，可纠正默认的 SQL Server 或 Azure SQL 数据库行为对于特定客户工作负荷不完善的情况。

下一个示例演示查询处理器如何生成对于性能和资源要求并非最佳的计划。 此示例还显示，当使用查询提示时，可以减少 SQL 数据库的查询运行时间和资源需求：

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

该设置代码将创建一个包含偏斜数据分布的表。 根据要选择哪个参数，最佳查询计划会有所不同。 遗憾的是，计划缓存行为不会始终根据最常见的参数值重新编译查询。 因此，即使另一个计划平均而言是更好的计划选择，但很可能缓存非最佳计划并将其用于多个值。 然后，查询计划创建两个相同的存储过程，只不过其中一个存储过程包含特殊的查询提示。

**示例（第 1 部分）**

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

**示例（第 2 部分）**

（建议至少等待 10 分钟，再开始示例的第 2 部分，以便在所得的遥测数据中有不同结果。）

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

本例的每个部分均尝试将某个参数化插入语句运行 1,000 次（以产生足够多的负载以充当测试数据集）。 当执行存储过程时，查询处理器在其首次编译期间检查传递给过程的参数值（参数“探查”）。 处理器缓存所得的计划，并用于以后的调用，即使参数值不同也是如此。 可能无法在所有情况下均使用最佳计划。 有时，需要引导优化器选取更适合普通情况而非首次编译查询时的特定情况的计划。 在本例中，初始计划将生成一个“扫描”计划，读取所有行以查找与参数匹配的每个值：

![通过使用扫描计划优化查询](./media/sql-database-performance-guidance/query_tuning_1.png)

由于我们用值 1 执行该过程，因此所得的计划对于值 1 为最佳，但对于表中的所有其他值并非最佳。 如果随机选取每个计划，结果可能不是你期望的，因为计划执行得更慢，并使用更多资源。

如果通过将 `SET STATISTICS IO` 设置为 `ON` 来运行测试，则此示例中的逻辑扫描工作会在后台完成。 可以看到计划完成了 1,148 次读取（如果平均仅返回一行，此读取效率并不高）：

![通过使用逻辑扫描优化查询](./media/sql-database-performance-guidance/query_tuning_2.png)

本例的第二部分使用查询提示告知优化器在编译过程中使用某个特定值。 在此案例中，它会强制查询处理器忽略作为参数传递的值，而不是假定 `UNKNOWN`。 它引用的是表中具有平均频率的值（忽略偏斜）。 所得的计划是一个基于搜寻的计划，平均而言，它比此示例第 1 部分中的计划速度更快且使用资源更少：

![通过使用查询提示优化查询](./media/sql-database-performance-guidance/query_tuning_3.png)

可以查看 **sys.resource_stats** 表的影响（执行测试的时间与数据填充表的时间之间有延迟）。 对于本例，会在 22:25:00 时间范围内执行第 1 部分，在 22:35:00 执行第 2 部分。 越早时间范围使用的资源比越晚时间范围要多（因计划效率提高）。

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![查询优化示例结果](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> 虽然此示例特意选择了较小的卷，但非最佳参数的影响仍很大，对于较大的数据库尤为如此。 这种区别在极端情况下对于快速情况和慢速情况可在数秒和数小时之间。
> 
> 

可检查 **sys.resource_stats**，以确定测试使用的资源多于还是少于另一个测试。 在比较数据时，请使测试相隔一定时间，以使其不会在 **sys.resource_stats** 视图中的同一 5 分钟时间范围内重合。 本练习的目标是将使用的资源总量降至最低，而非将峰值资源降至最低。 一般而言，优化一段产生延迟的代码也将减少资源消耗。 请确保对应用程序进行的更改都是必需的，并且这些更改不会对可能在应用程序中使用查询提示的人员的客户体验产生负面影响。

如果工作负荷由一组重复的查询组成，则捕获并验证这些计划选择的最优性通常有意义，因为这样做会使托管数据库所需的资源大小单位降至最低。 对其进行验证后，应偶尔重新检查计划，帮助确保它未降级。 可以详细了解[查询提示 (TRANSACT-SQL)](https://msdn.microsoft.com/library/ms181714.aspx)。

### <a name="cross-database-sharding"></a>跨数据库分片
由于 Azure SQL 数据库运行在商用硬件上，因此，单一数据库的容量限制低于传统的本地 SQL Server 安装。 某些客户使用分片技术在数据库操作不符合 Azure SQL 数据库中单一数据库的限制时，将这些操作分摊到多个数据库上。 在 Azure SQL 数据库中使用分片技术的大多数客户将单个维度的数据拆分到多个数据库上。 对于该方法，需要了解 OLTP 应用程序执行的事务经常仅适用于架构中的一行或少数几行。

> [!NOTE]
> SQL 数据库现在提供一个库来帮助分片。 有关详细信息，请参阅[弹性数据库客户端库概述](sql-database-elastic-database-client-library.md)。
> 
> 

例如，如果数据库包含客户名称、订单和订单明细（如 SQL Server 附带的传统示例 Northwind 数据库），则可通过将客户与相关订单及订单明细集中在一起，将这些数据拆分到多个数据库中。 可以保证客户的数据保留在单一数据库中。 应用程序将不同的客户拆分到多个数据库上，实际上就是将负载分散在多个数据库上。 通过分片，客户不仅可以避免达到最大数据库大小限制，而且 Azure SQL 数据库还能够处理明显大于不同性能级别限制的工作负荷，前提是每个数据库适合其 DTU。

尽管数据库分片不会减少解决方案的总体资源容量，但最好支持将超大型解决方案分散在多个数据库中。 每个数据库可以在不同的性能级别运行，以支持非常大的、资源要求高的“有效”数据库。

### <a name="functional-partitioning"></a>功能分区
SQL Server 用户经常将许多功能集中在单一数据库内。 例如，如果应用程序包含管理商店库存的逻辑，则该数据库可能包含与库存、跟踪采购订单、管理月末报告的存储过程和索引或具体化视图相关的逻辑。 此技术的优点是可轻松管理备份等数据库操作，但它也要求优化硬件大小，以处理应用程序所有功能的峰值负载。

如果在 Azure SQL 数据库内使用扩大体系结构，最好将应用程序的不同功能拆分到不同的数据库中。 通过使用此技术，每个应用程序可独立地缩放。 随着应用程序变得更加繁忙（并且数据库负载不断增长），管理员可针对应用程序中的每项功能选择单独的性能级别。 在限制范围内，此体系结构使应用程序的规模可大于单个商用计算机可处理的规模，因为负载分散在多个计算机上。

### <a name="batch-queries"></a>批处理查询
对于通过大量、频繁的即席查询访问数据的应用程序，在应用层与 Azure SQL 数据库层之间的网络通信上花费了大量响应时间。 即使在应用程序与 Azure SQL 数据库同处一个数据中心时，大量数据访问操作也可能会增大二者之间的网络延迟。 要减少数据访问操作的网络往返次数，请考虑选择批处理即席查询，或者选择将其编译为存储过程。 如果批处理即席查询，可将多个查询作为一个大批次在一次行程中发送到 Azure SQL 数据库。 将即席查询编入存储过程，可获得与批处理相同的结果。 使用存储过程还有一个好处，即增加将查询计划缓存在 Azure SQL 数据库中的机会，以便再次执行同一存储过程。

某些应用程序频繁写入。 有时，通过考虑如何统一批处理写入，可以减少数据库上的总 I/O 负载。 通常，这与在存储过程和即席批中使用显式事务代替自动提交事务一样简单。 有关各种可用方法的评估，请参阅 [Azure 中 SQL 数据库应用程序的批处理技术](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx)。 使用自己的工作负荷进行试验，以找到适合批处理的模型。 请务必了解模型可能会有稍微不同的事务一致性保证。 要找到将资源用量降至最低的正确工作负荷，需要找到一致性与性能折中的正确组合。

### <a name="application-tier-caching"></a>应用程序层缓存
某些数据库应用程序包含具有大量读取操作的工作负荷。 缓存层可减少数据库上的负载，还有可能降低支持使用 Azure SQL 数据库的数据库所需的性能级别。 如果读取工作负荷较重，通过 [Azure Redis 缓存](https://azure.microsoft.com/services/cache/)，只需读取数据一次（也许只需对每个应用层计算机读取一次，具体取决于其配置方式），然后将该数据存储在 SQL 数据库外部。 这样可降低数据库负载（CPU 和读取 I/O），但对于事务一致性有影响，因为从缓存读取的数据可能与数据库中数据不同步。 虽然许多应用程序可接受一定的不一致，但并非所有工作负荷都是这样。 应该先完全了解任何应用程序要求，再实施应用程序层缓存策略。

## <a name="next-steps"></a>后续步骤
* 有关服务层的详细信息，请参阅 [SQL 数据库选项和性能](sql-database-service-tiers.md)
* 有关弹性池的详细信息，请参阅[什么是 Azure 弹性池？](sql-database-elastic-pool.md)
* 有关性能和弹性池的信息，请参阅[何时考虑弹性池](sql-database-elastic-pool-guidance.md)

