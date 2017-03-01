---
title: "Azure SQL 数据库的查询性能见解 | Microsoft 文档"
description: "查询性能监视可以识别 Azure SQL 数据库中 CPU 消耗最大的查询。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: c2f580b2-3835-453f-89f5-140e02dd2ea7
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ddf93a0db278fffa2aa45fdc73a1c7f57a84fb39
ms.openlocfilehash: 14140a6aa8c8cccf658f8e98305659e8ae76dcad


---
# <a name="azure-sql-database-query-performance-insight"></a>Azure SQL 数据库查询性能见解
管理和优化关系数据库性能是一项颇具挑战性的操作，需要投入大量的专业知识和时间。 查询性能见解通过提供以下内容减少数据库性能故障排除消耗的时间：

* 深入洞察数据库资源 (DTU) 的消耗。 
* 按 CPU/持续时间/执行计数获取热门查询，可以对这些查询进行调整来提高性能。
* 可以深入了解查询详情，查看其文本和资源使用历史记录。 
* 性能调优注释可以显示 [SQL Azure 数据库顾问](sql-database-advisor.md)执行的操作  



## <a name="prerequisites"></a>先决条件
* 查询性能见解要求已在数据库上启用 [Query Store](https://msdn.microsoft.com/library/dn817826.aspx)。 如果未运行查询存储，该门户会提示将其打开。

## <a name="permissions"></a>权限
需要具备以下[基于角色的访问控制](../active-directory/role-based-access-control-configure.md)权限才能使用查询性能见解： 

* 需要具备**读者**、**所有者**、**参与者**、**SQL 数据库参与者**或 **SQL Server 参与者**权限才能查看资源占用排名靠前的查询和图表。 
* 需要具备**所有者**、**参与者**、**SQL 数据库参与者**或 **SQL Server 参与者**权限才能查看查询文本。

## <a name="using-query-performance-insight"></a>使用查询性能见解
查询性能见解很容易使用：

* 打开 [Azure 门户](https://portal.azure.com/)并找到要检查的数据库。 
  * 在左侧菜单的“支持和疑难解答”下，选择“查询性能见解”。
* 在第一个选项卡上，查看资源占用排名靠前的查询列表。
* 选择单个查询以查看其详细信息。
* 打开 [SQL Azure 数据库顾问](sql-database-advisor.md)并检查是否存在任何可用的建议。
* 使用滑块或缩放图标更改检测间隔。
  
    ![性能仪表板](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Query Store 需要捕获数小时的数据才能使 SQL 数据库提供查询性能见解。 如果在某段时间内数据库没有任何活动，或查询存储不处于活动状态，则在显示该期间时图表是空的。 如果查询存储未运行，你随时可以启用它。   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>查看 DTU 消耗量靠前的查询
在[门户](http://portal.azure.com)中执行以下操作：

1. 浏览至 SQL 数据库并单击“**所有设置**” > “**支持 + 疑难解答**” > “**查询性能见解**”。 
   
    ![查询性能见解][1]
   
    排位靠前的查询视图随即打开并列出 CPU 消耗量靠前的查询。
2. 单击图表周围以获取详细信息。<br>最前面一行显示数据库的整体 DTU 百分比，条形显示所选查询在所选时间间隔内消耗的 CPU 百分比（例如，如果选择了“**过去一周**”，则每个条代表&1; 天）。
   
    ![排名靠前的查询][2]
   
    底部网格表示可见查询的聚合信息。
   
   * 查询 ID - 数据库内查询的唯一标识符。
   * 每个查询在可观察到的时间间隔内的 CPU（取决于聚合函数）。
   * 每个查询的持续时间（取决于聚合函数）。
   * 特定查询的执行总次数。
     
     使用复选框选择或清除图表要包含或排除的单个查询。
3. 如果感觉数据已过时，可单击“**刷新**”按钮。
4. 可以使用滑块和缩放按钮更改检测间隔及检查峰值： ![设置](./media/sql-database-query-performance/zoom.png)
5. 或者，如果想要查看不同的视图，则可以选择“**自定义**”选项卡并设置：
   
   * 度量值（CPU、持续时间、执行计数）
   * 时间间隔（过去 24 小时、过去一周、过去一个月）。 
   * 查询数。
   * 聚合函数。
     
     ![设置](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>查看单个查询的详细信息
若要查看查询详细信息，请执行以下操作：

1. 单击排位靠前查询所在列表中的任意查询。
   
    ![详细信息](./media/sql-database-query-performance/details.png)
2. 详细信息视图随即打开，查询 CPU 消耗/持续时间/执行计数已按时间细分。
3. 单击图表周围以获取详细信息。
   
   * 顶部图表显示含有总体数据库 DTU% 的折线图，条形是所选查询使用的 CPU%。
   * 第二个图表显示所选查询的总持续时间。
   * 底部图表显示所选查询的执行总数。
     
     ![查询详细信息][3]
4. 或者，可使用滑块、缩放按钮或单击“**设置**”以自定义查询数据显示方式或选择不同的时间段。

## <a name="review-top-queries-per-duration"></a>根据持续时间查看热门查询
在查询性能见解的最新更新中，我们引入了两个新度量值，他们可以帮助识别潜在的瓶颈：持续时间和执行计数。<br>

长时间运行的查询长时间锁定资源、阻止其他用户和限制可伸缩性的可能性最大。 他们也最适合进行优化。<br>

若要识别长时间运行的查询，请执行以下操作：

1. 在所选数据库的查询性能见解中打开“**自定义**”选项卡
2. 将度量值更改为“**持续时间**”
3. 选择查询数和检测间隔
4. 选择聚合函数
   
   * **Sum** 对整个检测间隔期间的所有查询执行时间进行求和。
   * **Max** 查找整个检测间隔中执行时间最长的查询。
   * **Avg** 查找所有查询执行的平均执行时间并显示平均值中的最高值。 
     
     ![查询持续时间][4]

## <a name="review-top-queries-per-execution-count"></a>根据执行计数查看热门查询
执行数高可能不会影响数据库本身，资源使用率可能偏低，但是整个应用程序可能会变慢。

某些情况下，执行计数非常高可能会导致网络往返增加。 往返对性能存在重大影响。 他们受网络延迟和下游服务器延迟的制约。 

例如，许多数据驱动的网站大量访问每个用户请求的数据库。 连接池会有所帮助，增加的网络流量和处理数据库服务器上的负载可能会对性能产生消极影响。  通常建议将往返保持在绝对极小值。

若要识别频繁执行的查询，即（“聊天式”）查询：

1. 在所选数据库的查询性能见解中打开“**自定义**”选项卡
2. 将度量值更改为“**执行计数**”
3. 选择查询数和检测间隔
   
    ![查询执行计数][5]

## <a name="understanding-performance-tuning-annotations"></a>了解性能调优注释
在查询性能见解中查看工作负荷时，可能会注意到图表顶部上带有竖线的图标。<br>

这些图标是注释；他们代表 [SQL Azure 数据库顾问](sql-database-advisor.md)执行的影响性能的操作。 通过悬停注释，可以获取操作的基本信息：

![查询注释][6]

如果想要了解详细信息或应用顾问建议，请单击该图标。 它会打开操作详情。 如果是可用建议则可使用命令直接应用该建议。

![查询注释详情][7]

### <a name="multiple-annotations"></a>多个注释。
由于缩放级别而导致距离彼此较近的注释折叠为一个注释，这是有可能的。 这会由特殊图标表示，单击该图标则会打开新的边栏选项卡，里面会显示分组注释列表。
关联查询和性能调优操作可以帮助更好地了解自己的工作负荷。 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>优化查询性能见解的“查询存储”配置
在使用查询性能见解的过程中，你可能会遇到以下查询存储消息：

* “此数据库上的 Query Store 未正确配置。 请单击此处了解详细信息。”
* “此数据库上的 Query Store 未正确配置。 单击此处更改设置。” 

当查询存储无法收集新数据时，通常会显示这些消息。 

Query Store 处于只读状态且将参数设置为最优时会发生第一种情况。 可以通过增加 Query Store 大小或清除 Query Store 来修复此问题。

![qds 按钮][8]

Query Store 处于关闭状态或参数未设置为最优时会发生第二种情况。 <br>可以通过执行下方命令或直接通过门户来更改保留和捕获策略并启用 Query Store：

![qds 按钮][9]

### <a name="recommended-retention-and-capture-policy"></a>建议的保留和捕获策略
有两种类型的保留策略：

* 基于大小的保留策略 - 如果设置为“自动”，则会在快要达到最大大小时自动清除数据。
* 基于时间的保留策略 - 默认设置为 30 天。也就是说，如果空间不足，查询存储会删除 30 天以前的查询信息

可以将捕获策略设置为：

* **全部** - 捕获所有查询。
* **自动** - 忽略不频繁的查询以及编译和执行持续时间很短的查询。 执行计数、编译和运行时持续时间的阈值由内部决定。 这是默认选项。
* **无** – Query Store 停止捕获新查询，但是仍然收集已捕获查询的运行时统计信息。

建议将所有策略设置为“自动”，将清除策略设置为“30 天”：

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

增加查询存储的大小。 为此，可在连接到数据库以后发出以下查询：

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

应用这些设置最终会使 Query Store 收集新查询，但是如果不想等待则可清除 Query Store。 

> [!NOTE]
> 执行以下查询会删除当前位于查询存储中的所有信息。 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>摘要
查询性能见解可帮助你了解查询工作负荷的影响，以及它与数据库资源消耗的关系。 使用此功能可以了解排名靠前的消耗查询，并在发生问题之前轻松找出问题。

## <a name="next-steps"></a>后续步骤
有关改善 SQL 数据库性能的其他建议，请单击“**查询性能见解**”边栏选项卡上的“[建议](sql-database-advisor.md)”。

![Performance Advisor](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png




<!--HONumber=Jan17_HO5-->


