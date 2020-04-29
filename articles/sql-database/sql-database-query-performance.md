---
title: Query Performance Insight
description: 查询性能监视可为 Azure SQL 数据库中的单一数据库和共用数据库确定最耗 CPU 且运行时间最长的查询。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: f5998fde6659715de4fcb533cb0f41a8939b1c48
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79214063"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>适用于 Azure SQL 数据库的 Query Performance Insight

Query Performance Insight 为单一数据库和共用数据库提供了智能查询分析。 它有助于确定工作负荷中资源消耗最多且运行时间最长的查询。 这可帮助你找到要优化的查询，以提高整体工作负荷性能并有效使用要支付的资源。 Query Performance Insight 通过提供以下功能，帮助减少排查数据库性能问题所花费的时间：

* 深入了解数据库资源 (DTU) 消耗
* 有关按 CPU、持续时间和执行计数列出的热门数据库查询的详细信息（用于性能改进的潜在优化候选项）
* 可以深入到查询详细信息，以查看查询文本和资源利用率历史记录
* 用于显示[数据库顾问](sql-database-advisor.md)提供的性能建议的注释

![Query Performance Insight](./media/sql-database-query-performance/opening-title.png)

## <a name="prerequisites"></a>先决条件

查询性能见解要求已在数据库上启用 [Query Store](https://msdn.microsoft.com/library/dn817826.aspx)。 默认情况下，会自动为所有 Azure SQL 数据库启用查询存储。 如果查询存储未运行，Azure 门户将提示你启用它。

> [!NOTE]
> 如果门户中显示“未在此数据库中正确配置查询存储”消息，请参阅[优化查询存储的配置](#optimize-the-query-store-configuration)。

## <a name="permissions"></a>权限

需要拥有以下[基于角色的访问控制](../role-based-access-control/overview.md)权限才能使用 Query Performance Insight：

* 需要拥有“读取者”、“所有者”、“参与者”、“SQL DB 参与者”或“SQL Server 参与者”权限才能查看资源消耗量靠前的查询和图表。     
* 需要具备**所有者**、**参与者**、**SQL DB 参与者**或 **SQL Server 参与者**权限才能查看查询文本。

## <a name="use-query-performance-insight"></a>使用 Query Performance Insight

Query Performance Insight 很容易使用：

1. 打开 [Azure 门户](https://portal.azure.com/)并找到要检查的数据库。
2. 在左侧菜单中，打开“智能性能” > “Query Performance Insight”。  
  
   ![菜单中的“Query Performance Insight”](./media/sql-database-query-performance/tile.png)

3. 在第一个选项卡上，查看资源占用排名靠前的查询列表。
4. 选择单个查询以查看其详细信息。
5. 打开“智能性能” > “性能建议”，检查是否提供了任何性能建议。   有关内置性能建议的详细信息，请参阅 [SQL 数据库顾问](sql-database-advisor.md)。
6. 使用滑块或缩放图标更改检测间隔。

   ![性能仪表板](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> 要使 SQL 数据库在 Query Performance Insight 中呈现信息，查询存储需要捕获几个小时的数据。 如果在某段时间内数据库不活动或查询存储不活动，则 Query Performance Insight 在显示该时间范围时，图表将是空的。 如果查询存储未运行，随时可以启用它。 有关详细信息，请参阅[有关查询存储的最佳做法](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)。
>

若要查看数据库性能建议，请在 Query Performance Insight 导航边栏选项卡上选择“[建议](sql-database-advisor.md)”。

![“建议”选项卡](./media/sql-database-query-performance/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>查看 CPU 消耗量靠前的查询

默认情况下，首次打开 Query Performance Insight 时，它会显示 CPU 消耗量靠前的五个查询。

1. 使用复选框选择或清除要在图表中包含或排除的各个查询。

   最上面一行显示数据库的总体 DTU 百分比。 条形显示所选查询在所选间隔内消耗的 CPU 百分比。 例如，如果选择了“过去一周”，则每个条形代表一天。 

   ![消耗量靠前的查询](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > 显示的 DTU 线条将与一小时期限内的最大消耗值相聚合。 此信息仅供与查询执行统计信息进行高级比较。 在某些情况下，与执行的查询相比，DTU 利用率看似过高，但事实可能并非如此。
   >
   > 例如，如果某个查询只是在几分钟时间内利用了 100% 的 DTU，Query Performance Insight 中的 DTU 线条会将整个小时的消耗量显示为 100%（最大聚合值的结果）。
   >
   > 若要进行更精细的比较（一分钟精度），请考虑创建自定义的 DTU 利用率图表：
   >
   > 1. 在 Azure 门户中，选择“Azure SQL 数据库” > “监视”。  
   > 2. 选择“指标”  。
   > 3. 选择“+添加图表”  。
   > 4. 在图表上选择 DTU 百分比。
   > 5. 另外，请左上方的菜单中选择“过去 24 小时”，并将其更改为 1 分钟。 
   >
   > 使用详细程度更高的自定义 DTU 图表来与查询执行图表进行比较。

   底部网格显示可见查询的聚合信息：

   * 查询 ID（数据库中的查询的唯一标识符）。
   * 每个查询在可观测的间隔内消耗的 CPU（取决于聚合函数）。
   * 每个查询的持续时间（也取决于聚合函数）。
   * 特定查询的执行总次数。

2. 如果数据已过时，请选择“刷新”按钮。 

3. 使用滑块和缩放按钮来更改观测间隔和调查消耗峰值：

   ![用于更改间隔的滑块和缩放按钮](./media/sql-database-query-performance/zoom.png)

4. 或者，可以选择“自定义”选项卡来自定义以下属性的视图： 

   * 指标（CPU、持续时间、执行计数）。
   * 时间间隔（过去 24 小时、过去一周或过去一个月）。
   * 查询数。
   * 聚合函数。
  
   ![“自定义”选项卡](./media/sql-database-query-performance/custom-tab.png)
  
5. 选择“转到 >”按钮查看自定义的视图。 

   > [!IMPORTANT]
   > Query Performance Insight 只能显示消耗量靠前的 5 到 20 个查询，具体取决于所做的选择。 数据库运行的查询数可能远远超过显示的查询数，但图表中不包括这些多出的查询。
   >
   > 某种数据库工作负荷类型中可能存在大量的小型查询，其数目超过了显示的数目，并且它们频繁运行，占用了大部分 DTU。 这些查询也不会显示在性能图表上。
   >
   > 例如，某个查询可能在一段时间内消耗了大量的 DTU，不过，在观测期内，其总消耗量小于其他消耗量靠前的查询。 在这种情况下，此查询的资源利用率不会显示在图表上。
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

3. 或者，可使用滑块、使用缩放按钮或选择“设置”来自定义查询数据的显示方式，或选取其他时间范围****。

   > [!IMPORTANT]
   > Query Performance Insight 不捕获任何 DDL 查询。 在某些情况下，它可能不会捕获所有即席查询。
   >

## <a name="review-top-queries-per-duration"></a>根据持续时间查看热门查询

Query Performance Insight 中有两个指标可以帮助找到潜在瓶颈：持续时间和执行计数。

长时间运行的查询长时间锁定资源、阻止其他用户和限制可伸缩性的可能性最大。 它们还是最佳优化候选对象。

若要识别长时间运行的查询，请执行以下操作：

1. 在选定数据库的 Query Performance Insight 中打开“自定义”选项卡****。
2. 将指标更改为“持续时间”****。
3. 选择查询数和观察间隔。
4. 选择聚合函数：

   * **Sum** 对整个观察间隔内的所有查询执行时间进行求和。
   * **Max** 查找整个观察间隔内执行时间最长的查询。
   * **Avg** 查找所有查询执行的平均执行时间并显示平均值排名靠前的查询。

   ![查询持续时间](./media/sql-database-query-performance/top-duration.png)

5. 选择“转到 >”按钮以查看自定义视图****。

   > [!IMPORTANT]
   > 调整查询视图不会更新 DTU 行。 DTU 行始终显示间隔内的最大消耗值。
   >
   > 若要更详细地了解数据库 DTU 消耗（最多一分钟），请考虑在 Azure 门户中创建自定义图表：
   >
   > 1. 选择 " **Azure SQL 数据库** > **监视**"。
   > 2. 选择“指标”****。
   > 3. 选择“+添加图表”****。
   > 4. 在图表上选择 DTU 百分比。
   > 5. 此外，在左上方的菜单中选择“过去 24 小时”并将其更改为一分钟****。
   >
   > 我们建议使用自定义 DTU 图表与查询性能图表进行比较。
   >

## <a name="review-top-queries-per-execution-count"></a>根据执行计数查看热门查询

即使大量执行可能不会影响数据库本身并且资源使用率较低，使用数据库的用户应用程序也可能会变慢。

在某些情况下，高执行计数可能会导致网络往返增加。 往返会影响性能。 它们受网络延迟和下游服务器延迟的影响。

例如，许多数据驱动的网站为每个用户请求大量访问数据库。 虽然连接池会有所帮助，但数据库服务器上增加的网络流量和处理负载可能会降低性能。 一般而言，请将往返保持在最低限度。

识别频繁执行的（“聊天式”）查询：

1. 在选定数据库的 Query Performance Insight 中打开“自定义”选项卡****。
2. 将指标更改为“执行计数”****。
3. 选择查询数和观察间隔。
4. 选择“转到 >”按钮以查看自定义视图****。

   ![查询执行计数](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>了解性能调整注释

在 Query Performance Insight 中查看工作负载时，可能会注意到图表顶部带有竖线的图标。

这些图标是注释。 它们显示 [SQL 数据库顾问](sql-database-advisor.md)的性能建议。 通过将鼠标悬停在注释上，可以获得有关性能建议的摘要信息。

   ![查询注释](./media/sql-database-query-performance/annotation.png)

如果想要了解详细信息或想要应用顾问的建议，请选择图标以打开建议操作的详细信息。 如果这是可用建议，则可以从门户直接应用该建议。

   ![查询注释详细信息](./media/sql-database-query-performance/annotation-details.png)

在某些情况下，根据缩放级别，彼此靠近的注释可能会折叠为一个注释。 Query Performance Insight 以组注释图标表示这一情况。 选择组注释图标将打开列出注释的新边栏选项卡。

关联查询和性能调整操作可能会有助于更好地了解工作负载。

## <a name="optimize-the-query-store-configuration"></a>优化查询存储配置

使用 Query Performance Insight 时，可能会看到以下查询存储错误消息：

* “此数据库上的 Query Store 未正确配置。 请单击此处了解详细信息。”
* “此数据库上的 Query Store 未正确配置。 单击此处更改设置。”

当查询存储无法收集新数据时，通常会显示这些消息。

查询存储处于只读状态且参数设置为最优时会发生第一种情况。 可以通过增加查询存储的大小或清除查询存储来修复此问题。 （如果清除查询存储，则之前收集的所有遥测数据都将丢失。）

   ![查询存储详细信息](./media/sql-database-query-performance/qds-off.png)

查询存储处于未启用状态或参数未设置为最优时会发生第二种情况。 可以通过运行 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 或 Azure 门户提供的以下命令来更改保留和捕获策略，以及启用查询存储。

### <a name="recommended-retention-and-capture-policy"></a>建议的保留和捕获策略

有两种类型的保留策略：

* **基于大小**：如果此策略设置为 "**自动**"，则它将在达到最大大小接近最大值时自动清除数据。
* **基于时间**：默认情况下，此策略设置为30天。 如果查询存储空间不足，则会删除超过 30 天的查询信息。

可将捕获策略设置为：

* **All**：查询存储捕获所有查询。
* **自动**：查询存储忽略不频繁的查询和查询，而不包含无意义的编译和执行持续时间。 执行计数、编译持续时间和运行时持续时间的阈值在内部确定。 这是默认选项。
* **无**：查询存储停止捕获新查询，但仍收集已捕获查询的运行时统计信息。

我们建议通过从 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 或 Azure 门户执行以下命令，将所有策略设置为“自动”并将清理策略设置为 30 天****。 （将 `YourDB` 替换为数据库名称。）

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

通过 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 或 Azure 门户连接到数据库并运行以下查询，增加查询存储的大小。 （将 `YourDB` 替换为数据库名称。）

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

应用这些设置最终会让查询存储收集新查询的遥测数据。 如果需要查询存储能够立即运行，则可以选择通过 SSMS 或 Azure 门户运行以下查询来清除查询存储。 （将 `YourDB` 替换为数据库名称。）

> [!NOTE]
> 运行以下查询将删除查询存储中之前收集的所有监视遥测数据。

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>后续步骤

请考虑使用[Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)对大量的单一数据库和池数据库、弹性池、托管实例和实例数据库进行高级性能监视。
