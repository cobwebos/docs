---
title: "通过用于 Visual Studio 的 Azure Data Lake 工具解决数据倾斜问题 | Microsoft 文档"
description: "通过用于 Visual Studio 的 Azure Data Lake 工具排查潜在解决方案的数据倾斜问题。"
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/16/2016
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: 4ed240c0e636bb0b482c103bbe8462d86769ecc3
ms.openlocfilehash: 13fa1bc8278460c1195ec553c32ff79d11240be3
ms.lasthandoff: 01/05/2017


---

# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>通过用于 Visual Studio 的 Azure Data Lake 工具解决数据倾斜问题

## <a name="what-is-data-skew"></a>什么是数据倾斜？

简而言之，数据倾斜就是某个值的数据分布不均衡。 假设你指定了 50 名税务检查员来审核退税，每个州（美国）一位检查员。 怀俄明州的检查员几乎没什么事，因为该州的人口很少。 而加利福尼亚州的检查员则忙碌异常，因为该州的人口很多。
    ![数据倾斜问题示例](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

在上面的情形中，数据在所有税务检查员之间的分配并不均衡，也就是说，某些检查员的工作量超出其他检查员。 在你自己的作业中，你会经常遇到类似此处例举的税务检查员的情形。 用更技术化的术语来说，数据倾斜是指一个顶点获得的数据量远远超出其对等项，导致该顶点的工作量超出其他顶点，最终延缓了整个作业的完成速度。 更糟的是，作业可能会失败，因为顶点可能存在限制，例如 5 小时的运行时限制和 6 GB 的内存限制。

## <a name="resolving-data-skew-problems"></a>解决数据倾斜问题

用于 Visual Studio 的 Azure Data Lake 工具可以帮助检测作业中是否存在数据倾斜问题。 如果存在问题，可以尝试用本部分的解决方案来解决。

### <a name="solution-1-improve-table-partitioning"></a>解决方法 1：改善表分区

#### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>选项 1：提前筛选偏斜的键值

如果不影响业务逻辑的话，可以提前筛选高频值。 例如，如果在 GUID 列中有大量的 000-000-000，则可能不需要聚合该值。 在聚合之前，可以编写“WHERE GUID != “000-000-000””来筛选高频值。

#### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>选项 2：选择不同的分区或分布键

在前面的示例中，如果只需检查全国的税务审核工作负荷，则可选择 ID 号作为键来改进数据分布。 有时，选择不同的分区或分布键可以更均衡地分配数据，但需确保该选择不会影响业务逻辑。 例如，若要计算每个州的税收总额，则可能需指定 _State_ 作为分区键。 如果仍遇到此问题，请尝试使用选项 3。

#### <a name="option-3-add-more-partition-or-distribution-keys"></a>选项 3：添加更多的分区键或分布键

可以使用多个键进行分区，而不是仅使用 _State_ 作为分区键。 例如，可以考虑添加 _ZIP Code_ 作为额外的分区键，减小数据分区大小，使数据分布更均衡。

#### <a name="option-4-use-round-robin-distribution"></a>选项 4：使用轮循机制分布

如果找不到适当的分区和分布键，可以尝试使用轮循机制分布。 轮循机制分布可以均匀处理所有行，并将其随机放入相应的桶中。 数据将均匀分布，但会丢失位置信息，因此也会在某些操作中降低作业性能，这是其缺点。 此外，如果仍要对倾斜的键执行聚合，则还会出现数据倾斜问题。 若要详细了解轮循机制分布，请参阅 [CREATE TABLE (U-SQL): Creating a Table with Schema](https://msdn.microsoft.com/en-us/library/mt706196.aspx#dis_sch)（CREATE TABLE (U-SQL)：使用架构创建表）的“U-SQL Table Distributions”（U-SQL 表分布）部分。

### <a name="solution-2-improve-the-query-plan"></a>解决方案 2：改进查询计划

#### <a name="option-1-use-the-create-statistics-statement"></a>选项 1：使用 CREATE STATISTICS 语句

U-SQL 提供针对表的 CREATE STATISTICS 语句。 此语句为查询优化器提供存储在表中的数据特征（例如值分布）的相关详细信息。 对于大多数查询，查询优化器已生成高质量查询计划所必需的统计信息。 有时候，用户可能需要通过 CREATE STATISTICS 或通过修改查询设计创建额外的统计信息，以便改进查询性能。 有关详细信息，请参阅 [CREATE STATISTICS (U-SQL)](https://msdn.microsoft.com/en-us/library/azure/mt771898.aspx) 页。

代码示例：

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>统计信息不会自动更新。 如果在不重新创建统计信息的情况下更新表中的数据，查询性能可能会降低。

#### <a name="option-2-use-skewfactor"></a>选项 2：使用 SKEWFACTOR

如果需要对每个州的税收数据进行汇总，则必须使用“GROUP BY state”方法，但此方法无法避免数据倾斜问题。 但是，可以在查询中提供数据提示来识别键中的数据倾斜，以便优化器为你准备执行计划。

通常可将参数设置为 0.5 和 1。0.5 表示倾斜不严重，1 表示严重倾斜。 由于提示会影响当前语句以及所有下游语句的执行计划优化，因此请确保在执行可能有倾斜的键范围聚合之前添加提示。

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

代码示例：

    //Add a SKEWFACTOR hint.
    @Impressions =
        SELECT * FROM
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions =
        SELECT
            ClientId,
            Query,
            SUM(PageClicks) AS Clicks
        FROM
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    //Query 2 for Key: Query
    @Display =
        SELECT * FROM @Sessions
            INNER JOIN @Campaigns
                ON @Sessions.Query == @Campaigns.Query
        ;   

如果你知道其他联接的行集较小，则除了添加适用于特定倾斜键联接案例的 SKEWFACTOR，还可以在 U-SQL 语句中将 ROWCOUNT 提示添加到 JOIN 之前，以便告知优化器这一点。 这样一来，优化器就可以选择广播联接策略，以便改进性能。 请注意，ROWCOUNT 并不解决数据倾斜问题，但可提供一些额外的帮助。

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

代码示例：

    //Unstructured (24-hour daily log impressions)
    @Huge   = EXTRACT ClientId int, ...
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    //Small subset (that is, ForgetMe opt out)
    @Small  = SELECT * FROM @Huge
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    //Result (not enough information to determine simple broadcast JOIN)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

### <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>解决方案 3：改进用户定义的化简器和组合器

有时，你可以编写用户定义的运算符来处理复杂的过程逻辑，精心编写的化简器和组合器在某些情况下可以缓解数据倾斜问题。

#### <a name="option-1-use-a-recursive-reducer-if-possible"></a>选项 1：在可能的情况下使用递归化简器

默认情况下，用户定义的化简器将以非递归模式运行，这意味着，键的化简工作将分布到单个顶点。 但如果数据倾斜，则巨型数据集可能会在单个顶点中处理并长时间运行。

若要提高性能，可在代码中添加属性，以便定义在递归模式下运行的化简器。 然后，可将巨型数据集分配到多个顶点并以并行方式运行，加快作业速度。

若要将非递归化简器更改为递归型，需确保算法是关联性的。 例如，求和是关联性，而求中间值则不是。 此外，需确保化简器的输入和输出保持相同的架构。

递归化简器的属性：

    [SqlUserDefinedReducer(IsRecursive = true)]

代码示例：

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

#### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>选项 2：在可能的情况下使用行级组合器模式

类似于特定倾斜键联接案例中的 ROWCOUNT 提示，组合器模式会尝试将巨型倾斜键值集分配到多个顶点，使工作可以并发执行。 组合器模式不能解决数据倾斜问题，而只能针对巨型倾斜键值集提供一定的辅助。

默认情况下，组合器模式为 Full，表示左行集与右行集不能分隔。 将模式设置为“Left/Right/Inner”可以启用行级联接。 系统会分隔相应的行集，并将它们分配到多个并行运行的顶点中。 但是，在配置组合器模式之前需谨慎行事，确保相应的行集可以分隔。

以下示例显示了一个分隔的左行集。 每个输出行依赖于左侧的单个输入行，并可能依赖于右侧包含相同键值的所有行。 如果将组合器模式设置为 Left，系统会将巨型左行集分隔成小型行集，并将其分配到多个顶点。

![组合器模式示意图](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>如果设置了错误的组合器模式，则组合效率较低，而且结果可能错误。

组合器模式的属性：

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left)：每个输出行依赖于左侧的单个输入行（还可能依赖于右侧包含相同键值的所有行）。

- qlUserDefinedCombiner(Mode=CombinerMode.Right)：每个输出行依赖于右侧的单个输入行（还可能依赖于左侧包含相同键值的所有行）。

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner)：每个输出行依赖于左侧和右侧包含相同值的单个输入行。

代码示例：

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }

