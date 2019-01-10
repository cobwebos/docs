---
title: 适用于 Azure SQL 数据库的 Query Performance Insight | Microsoft Docs
description: 查询性能监视可以识别 Azure SQL 数据库中 CPU 消耗最大的查询。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 5d892005881436dec89c0d0d010f7f02e7bdebf9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039191"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>适用于 Azure SQL 数据库的 Query Performance Insight

管理和调整关系数据库的性能需要拥有专业知识并投入时间。 Query Performance Insight 是 Azure SQL 数据库智能性能产品系列的一部分， 它通过提供以下功能，帮助减少对数据库性能进行故障排除所需消耗的时间：

* 深入洞察数据库资源 (DTU) 的消耗。
* 按 CPU、持续时间和执行计数列出排名靠前的数据库查询的详细信息（潜在性能改进调整候选对象）。
* 向下钻取查询的详细信息的能力，以查看查询文本和资源利用率的历史记录。
* 显示 [SQL 数据库顾问](sql-database-advisor.md)的性能建议的注释。

![查询性能见解](./media/sql-database-query-performance/opening-title.png)

> [!TIP]
> 如需对 Azure SQL 数据库进行基本性能监视，我们建议使用 Query Performance Insight。 请注意本文中发布的产品限制。 如需对数据库性能进行大规模高级监视，我们建议使用 [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)。 它具有可实现自动化性能故障排除的内置智能。 若要自动调整一些最常见的数据库性能问题，我们建议使用[自动调整](sql-database-automatic-tuning.md)。

## <a name="prerequisites"></a>先决条件

查询性能见解要求已在数据库上启用 [Query Store](https://msdn.microsoft.com/library/dn817826.aspx)。 默认情况下，自动为所有 Azure SQL 数据库启用。 如果查询存储未运行，Azure 门户将提示启用它。

> [!NOTE]
> 如果门户中显示“查询存储未在此数据库上正确配置”消息，请参阅[优化查询存储配置](#optimize-the-query-store-configuration-for-query-performance-insight)。
>

## <a name="permissions"></a>权限

需要具备以下[基于角色的访问控制](../role-based-access-control/overview.md)权限才能使用 Query Performance Insight：

* 需要具备**读者**、**所有者**、**参与者**、**SQL 数据库参与者**或 **SQL Server 参与者**权限才能查看资源占用排名靠前的查询和图表。
* 需要具备**所有者**、**参与者**、**SQL 数据库参与者**或 **SQL Server 参与者**权限才能查看查询文本。

## <a name="use-query-performance-insight"></a>使用 Query Performance Insight

查询性能见解很容易使用：

1. 打开 [Azure 门户](https://portal.azure.com/)并找到要检查的数据库。
2. 在左侧菜单中，打开“智能性能” > “Query Performance Insight”。
  
   ![菜单上的 Query Performance Insight](./media/sql-database-query-performance/tile.png)

3. 在第一个选项卡上，查看资源占用排名靠前的查询列表。
4. 选择单个查询以查看其详细信息。
5. 打开“智能性能” > “性能建议”，并检查是否有任何性能建议可用。 有关内置性能建议的详细信息，请参阅 [SQL 数据库顾问](sql-database-advisor.md)。
6. 使用滑块或缩放图标更改观察间隔。

   ![性能仪表板](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> 若要让 SQL 数据库在 Query Performance Insight 中呈现信息，查询存储需要捕获数小时的数据。 如果在某段时间内数据库没有活动，或查询存储不处于活动状态，则当 Query Performance Insight 显示该时间范围时，图表将为空。 如果查询存储未运行，可随时启用它。 有关详细信息，请参阅[查询存储最佳做法](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)。

## <a name="review-top-cpu-consuming-queries"></a>查看 CPU 消耗靠前的查询

默认情况下，Query Performance Insight 会在首次打开时显示 CPU 消耗排名前五的查询。

1. 使用复选框选择或清除单个查询，以在图表中包含或排除它们。

    第一行显示数据库的整体 DTU 百分比。 条形图显示选定查询在选定间隔内消耗的 CPU 的百分比。 例如，如果选择“过去一周”，则每个条形代表一天。

    ![排名靠前的查询](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > 显示的 DTU 行经过聚合，得出一个小时内的最大消耗值。 它仅用于与查询执行统计信息进行高级比较。 在某些情况下，相较执行的查询，DTU 利用率可能会看起来过高，但实际情况可能并非如此。
   >
   > 例如，即使查询消耗的 DTU 达到 100% 上限仅几分钟时间，Query Performance Insight 中的 DTU 行也会将一个小时的消耗显示为 100%（最大聚合值的结果）。
   >
   > 如需进行更精细的比较（最多一分钟），请考虑创建自定义 DTU 利用率图表：
   >
   > 1. 在 Azure 门户中，选择“Azure SQL 数据库” > “监视”。
   > 2. 选择“指标”。
   > 3. 选择“+添加图表”。
   > 4. 在图表上选择 DTU 百分比。
   > 5. 此外，在左上方的菜单中选择“过去 24 小时”并将其更改为一分钟。
   >
   > 使用具有更精细级别详细信息的自定义 DTU 图表与查询执行图表进行比较。

   底部网格显示可见查询的聚合信息：

   * 查询 ID，数据库中查询的唯一标识符。
   * 可观察间隔内每个查询的 CPU 消耗，这取决于聚合函数。
   * 每个查询的持续时间，这也取决于聚合函数。
   * 特定查询的总执行次数。

2. 如果数据过时，请选择“刷新”按钮。

3. 使用滑块和缩放按钮更改观察间隔并检查消耗峰值：

   ![用于更改间隔的滑块和缩放按钮](./media/sql-database-query-performance/zoom.png)

4. （可选）可以选择“自定义”选项卡来自定义以下视图：

   * 指标（CPU、持续时间、执行计数）。
   * 时间间隔（过去 24 小时、过去一周、过去一个月）。
   * 查询数。
   * 聚合函数。
  
   ![“自定义”选项卡](./media/sql-database-query-performance/custom-tab.png)
  
5. 选择“转到 >”按钮以查看自定义视图。

   > [!IMPORTANT]
   > Query Performance Insight 被限制为显示消耗排名前五到排名前二十的查询，具体取决于所做的选择。 数据库可以运行超出所示排名名次数量的查询，但这些查询将不会包含在图表中。
   >
   > 可能存在一种数据库工作负载类型，其中除了所示排名靠前的查询外，还有大量较小的查询频繁运行并使用大部分 DTU。 这些查询不会在性能图表中显示。
   >
   > 例如，某个查询在一段时间内可能已经消耗了大量 DTU，但它在观察期间的总消耗低于其他消耗排名靠前的查询。 在这种情况下，此查询的资源利用率不会在图表上显示。
   >
   > 如果需要了解超出 Query Performance Insight 限制的排名靠前查询的执行情况，请考虑使用 [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) 进行高级数据库性能监视和故障排除。
   >

## <a name="view-individual-query-details"></a>查看单个查询的详细信息

若要查看查询详细信息，请执行以下操作：

1. 选择排位靠前查询列表中的任意查询。

    ![排名靠前查询列表](./media/sql-database-query-performance/details.png)

   随即打开一个详细视图。 它显示一段时间内的 CPU 消耗、持续时间和执行计数。

2. 选择图表功能以获取详细信息。

   * 顶部图表显示的一行中包含整体数据库 DTU 百分比。 条形图显示选定查询消耗的 CPU 百分比。
   * 第二个图表显示选定查询的总持续时间。
   * 底部图表显示选定查询的总执行次数。

   ![查询详细信息](./media/sql-database-query-performance/query-details.png)

3. 或者，可使用滑块、使用缩放按钮或选择“设置”来自定义查询数据的显示方式，或选取其他时间范围。

   > [!IMPORTANT]
   > Query Performance Insight 不捕获任何 DDL 查询。 在某些情况下，它可能不会捕获所有即席查询。
   >

## <a name="review-top-queries-per-duration"></a>根据持续时间查看热门查询

Query Performance Insight 中有两个指标可以帮助找到潜在瓶颈：持续时间和执行计数。

长时间运行的查询长时间锁定资源、阻止其他用户和限制可伸缩性的可能性最大。 它们还是最佳优化候选对象。

若要识别长时间运行的查询，请执行以下操作：

1. 在选定数据库的 Query Performance Insight 中打开“自定义”选项卡。
2. 将指标更改为“持续时间”。
3. 选择查询数和观察间隔。
4. 选择聚合函数：

   * **Sum** 对整个观察间隔内的所有查询执行时间进行求和。
   * **Max** 查找整个观察间隔内执行时间最长的查询。
   * **Avg** 查找所有查询执行的平均执行时间并显示平均值排名靠前的查询。

   ![查询持续时间](./media/sql-database-query-performance/top-duration.png)

5. 选择“转到 >”按钮以查看自定义视图。

   > [!IMPORTANT]
   > 调整查询视图不会更新 DTU 行。 DTU 行始终显示间隔内的最大消耗值。
   >
   > 若要更详细地了解数据库 DTU 消耗（最多一分钟），请考虑在 Azure 门户中创建自定义图表：
   >
   > 1. 选择“Azure SQL 数据库” > “监视”。
   > 2. 选择“指标”。
   > 3. 选择“+添加图表”。
   > 4. 在图表上选择 DTU 百分比。
   > 5. 此外，在左上方的菜单中选择“过去 24 小时”并将其更改为一分钟。
   >
   > 我们建议使用自定义 DTU 图表与查询性能图表进行比较。
   >

## <a name="review-top-queries-per-execution-count"></a>根据执行计数查看热门查询

即使大量执行可能不会影响数据库本身并且资源使用率较低，使用数据库的用户应用程序也可能会变慢。

在某些情况下，高执行计数可能会导致网络往返增加。 往返会影响性能。 它们受网络延迟和下游服务器延迟的影响。

例如，许多数据驱动的网站为每个用户请求大量访问数据库。 虽然连接池会有所帮助，但数据库服务器上增加的网络流量和处理负载可能会降低性能。 一般而言，请将往返保持在最低限度。

若要识别频繁执行的（“聊天式”）查询，请执行以下操作：

1. 在选定数据库的 Query Performance Insight 中打开“自定义”选项卡。
2. 将指标更改为“执行计数”。
3. 选择查询数和观察间隔。
4. 选择“转到 >”按钮以查看自定义视图。

   ![查询执行计数](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>了解性能调整注释

在 Query Performance Insight 中查看工作负载时，可能会注意到图表顶部带有竖线的图标。

这些图标是注释。 它们显示 [SQL 数据库顾问](sql-database-advisor.md)的性能建议。 通过将鼠标悬停在注释上，可以获得有关性能建议的摘要信息。

   ![查询注释](./media/sql-database-query-performance/annotation.png)

如果想要了解详细信息或想要应用顾问的建议，请选择图标以打开建议操作的详细信息。 如果这是可用建议，则可以从门户直接应用该建议。

   ![查询注释详细信息](./media/sql-database-query-performance/annotation-details.png)

在某些情况下，根据缩放级别，彼此靠近的注释可能会折叠为一个注释。 Query Performance Insight 以组注释图标表示这一情况。 选择组注释图标将打开列出注释的新边栏选项卡。

关联查询和性能调整操作可能会有助于更好地了解工作负载。

## <a name="optimize-the-query-store-configuration-for-query-performance-insight"></a>为 Query Performance Insight 优化查询存储配置

使用 Query Performance Insight 时，可能会看到以下查询存储错误消息：

* “此数据库上的 Query Store 未正确配置。 请单击此处了解详细信息。”
* “此数据库上的 Query Store 未正确配置。 单击此处更改设置。”

当查询存储无法收集新数据时，通常会显示这些消息。

查询存储处于只读状态且参数设置为最优时会发生第一种情况。 可以通过增加查询存储的大小或清除查询存储来修复此问题。 （如果清除查询存储，则之前收集的所有遥测数据都将丢失。）

   ![查询存储详细信息](./media/sql-database-query-performance/qds-off.png)

查询存储处于未启用状态或参数未设置为最优时会发生第二种情况。 可以通过运行 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 或 Azure 门户提供的以下命令来更改保留和捕获策略，以及启用查询存储。

### <a name="recommended-retention-and-capture-policy"></a>建议的保留和捕获策略

有两种类型的保留策略：

* **基于大小**：如果此策略设置为“自动”，则会在快要达到最大大小时自动清除数据。
* **基于时间**：默认情况下，此策略设置为 30 天。 如果查询存储空间不足，则会删除超过 30 天的查询信息。

可将捕获策略设置为：

* **全部**：查询存储会捕获所有查询。
* **自动**：查询存储会忽略不频繁的查询和包含不重要的编译和执行持续时间的查询。 执行计数、编译持续时间和运行时持续时间的阈值在内部确定。 这是默认选项。
* **无**：查询存储会停止捕获新查询，但仍会收集已捕获查询的运行时统计信息。

我们建议通过从 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 或 Azure 门户执行以下命令，将所有策略设置为“自动”并将清理策略设置为 30 天。 （将 `YourDB` 替换为数据库名称。）

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

通过 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 或 Azure 门户连接到数据库并运行以下查询，增加查询存储的大小。 （将 `YourDB` 替换为数据库名称。）

```T-SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

应用这些设置最终会让查询存储收集新查询的遥测数据。 如果需要查询存储能够立即运行，则可以选择通过 SSMS 或 Azure 门户运行以下查询来清除查询存储。 （将 `YourDB` 替换为数据库名称。）

> [!NOTE]
> 运行以下查询将删除查询存储中之前收集的所有监视遥测数据。

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="summary"></a>摘要

Query Performance Insight 可帮助了解查询工作负载的影响以及它与数据库资源消耗的关系。 借助此功能，可了解数据库中消耗排名靠前的查询，并且可在问题出现前找到要优化的查询。

## <a name="next-steps"></a>后续步骤

* 如需数据库性能建议，请在 Query Performance Insight 导航边栏选项卡中选择 [建议](sql-database-advisor.md)。

    ![“建议”选项卡](./media/sql-database-query-performance/ia.png)

* 考虑针对常见数据库性能问题启用 [自动调整](sql-database-automatic-tuning.md)。
* 了解[智能见解](sql-database-intelligent-insights.md)可如何帮助自动排除数据库性能问题。
* 考虑使用 [Azure SQL Analytics]( ../azure-monitor/insights/azure-sql.md) 对大量 SQL 数据库、弹性池和具有内置智能的托管实例进行高级性能监视。
