---
title: 监视查询
titleSuffix: Azure Cognitive Search
description: 监视性能和吞吐量的查询指标。 收集并分析诊断日志中的查询字符串输入。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: a3a313ef9cd74ba901f5a6a2d82a18e3c21145dc
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462509"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>监视 Azure 认知搜索中的查询请求

本文介绍如何使用指标和诊断日志记录来度量查询性能和卷。 它还说明了在需要评估搜索语料库的实用工具和有效性时，如何收集查询中使用的输入术语。

源为指标的历史数据将保留30天。 对于更长的保留期，或要报告操作数据和查询字符串，请确保启用指定存储选项的[诊断设置](search-monitor-logs.md)，以便保存已记录的事件和指标。

最大程度地提高数据度量的完整性的条件包括：

+ 使用可计费服务（在 "基本" 或 "标准" 级别创建的服务）。 免费服务由多个订阅者共享，这会在负载变化时引入一定数量的变动。

+ 如果可能，请使用单个副本和分区来创建包含和隔离环境。 如果使用多个副本，则会在多个节点之间平均查询指标，这可以降低结果的精度。 同样，多个分区表示数据被分割，如果索引也在进行，则可能存在某些分区可能具有不同数据的可能性。 优化查询性能时，单个节点和分区提供了更稳定的环境用于测试。

> [!Tip]
> 通过其他客户端代码和 Application Insights，还可以捕获点击链接型数据，深入了解应用程序用户感兴趣的 attracts。 有关详细信息，请参阅[搜索流量分析](search-traffic-analytics.md)。

## <a name="query-volume-qps"></a>查询卷（QPS）

卷按**每秒搜索查询数**（QPS）度量，这是一种内置的指标，可报告为在一分钟时间内执行的查询的平均、计数、最小值或最大值。 系统中的指标的一分钟间隔（TimeGrain = "PT1M"）是固定的。

查询通常以毫秒为单位执行，因此仅度量值为秒的查询将显示在度量值中。

| 聚合类型 | 说明 |
|------------------|-------------|
| 平均值 | 查询执行发生在一分钟内的平均秒数。|
| Count | 一分钟时间间隔内发送到日志的指标数。 |
| 最大值 | 一分钟内注册的每秒最大搜索查询数。 |
| 最小值 | 一分钟内每秒注册的最小搜索查询数。  |
| SUM | 在分钟内执行的所有查询的总和。  |

例如，在一分钟内，你可能具有如下所示的模式： SearchQueriesPerSecond 的最大值的1秒，后跟平均负载58秒，最后一秒只包含一个查询，这是最小值。

另一个示例：如果某个节点发出100个指标，其中每个指标的值为40，则 "Count" 为100，"Sum" 为4000，"Average" 是40，"Max" 是40。

## <a name="query-performance"></a>查询性能

在服务范围内，查询性能度量为搜索延迟（查询需要花费多长时间），并限制因资源争用而删除的查询。

### <a name="search-latency"></a>搜索延迟

| 聚合类型 | 延迟 | 
|------------------|---------|
| 平均值 | 平均查询持续时间（毫秒）。 | 
| Count | 一分钟时间间隔内发送到日志的指标数。 |
| 最大值 | 示例中运行时间最长的查询。 | 
| 最小值 | 示例中的最短运行查询。  | 
| 总计 | 示例中所有查询的总执行时间，在该时间间隔内执行（一分钟）。  |

请看下面的**搜索延迟**指标示例：86查询的采样，平均持续时间为23.26 毫秒。 最小值为0表示删除了某些查询。 运行时间最长的查询需要1000毫秒才能完成。 总执行时间为2秒。

![延迟聚合](./media/search-monitor-usage/metrics-latency.png "延迟聚合")

### <a name="throttled-queries"></a>限制查询

限制查询是指删除而不是进程的查询。 在大多数情况下，限制是运行服务的正常部分。  这并不一定表示出现了问题。

如果当前处理的请求数超过可用资源，则会发生限制。 当副本超出旋转或索引期间，你可能会看到限制请求的增加。 查询和索引请求均由同一组资源处理。

服务确定是否删除基于资源消耗的请求。 在一段时间内平均占用内存、CPU 和磁盘 IO 的资源的百分比。 如果此百分比超过阈值，则会限制对该索引的所有请求，直到减少请求量。 

根据你的客户端，可通过以下方式指示限制请求：

+ 服务返回错误：发送的请求太多。 请稍后再试。” 
+ 服务返回503错误代码，指示服务当前不可用。 
+ 如果你使用的是门户（例如搜索资源管理器），则会以静默方式删除查询，你将需要再次单击 "搜索"。

若要确认受限制的查询，请使用**限制搜索查询**度量值。 可以在门户中浏览指标，或创建警报指标，如本文所述。 对于在采样间隔内删除的查询，请使用*Total*来获取未执行的查询所占的百分比。

| 聚合类型 | 限制 |
|------------------|-----------|
| 平均值 | 间隔内删除的查询所占的百分比。 |
| Count | 一分钟时间间隔内发送到日志的指标数。 |
| 最大值 | 间隔内删除的查询所占的百分比。|
| 最小值 | 间隔内删除的查询所占的百分比。 |
| 总计 | 间隔内删除的查询所占的百分比。 |

对于受**限制的搜索查询百分比**、最小值、最大值、平均值和总计值，所有值都具有相同的值：已限制的搜索查询百分比，从搜索查询总数到一分钟。

在下面的屏幕截图中，第一个数字是计数（或发送到日志的指标数）。 显示在顶部或悬停于度量值上的其他聚合包括 average、maximum 和 total。 在此示例中，未删除任何请求。

![限制聚合](./media/search-monitor-usage/metrics-throttle.png "限制聚合")

## <a name="explore-metrics-in-the-portal"></a>在门户中探索指标

若要快速查看当前数字，"服务概述" 页上的 "**监视**" 选项卡显示了三个指标（**搜索延迟**，**每秒搜索查询数（按搜索单位）** ，**限制的搜索查询百分比**），以小时、天、周为单位，并选择更改聚合类型。

对于更深入的探索，从 "**监视**" 菜单中打开 "指标资源管理器"，以便可以分层、放大和可视化数据，从而探索趋势或异常情况。 有关指标资源管理器的详细信息，请参阅[创建指标图表教程](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer)。

1. 在 "监视" 部分下，选择 "**指标**" 以打开指标资源管理器，并将范围设置为搜索服务。

1. 在 "指标" 下，从下拉列表中选择一个，并查看首选类型的可用聚合列表。 聚合定义每个时间间隔内收集的值的采样方式。

   ![QPS 指标的指标资源管理器](./media/search-monitor-usage/metrics-explorer-qps.png "QPS 指标的指标资源管理器")

1. 在右上角，设置时间间隔。

1. 选择可视化效果。 默认值为折线图。

1. 通过选择 "**添加度量值**" 并选择不同聚合来层附加聚合。

1. 放大折线图上感兴趣的区域。 将鼠标指针放在区域的开头位置，单击并按住鼠标左键，拖动到区域的另一侧，然后松开按钮。 图表将在该时间范围内放大。

## <a name="identify-strings-used-in-queries"></a>标识在查询中使用的字符串

启用诊断日志记录时，系统将捕获**AzureDiagnostics**表中的查询请求。 作为先决条件，你必须已启用[诊断日志记录](search-monitor-logs.md)，并指定 log analytics 工作区或其他存储选项。

1. 在 "监视" 部分下，选择 "**日志**" 以打开 Log Analytics 中的空查询窗口。

1. 运行以下表达式，搜索查询。搜索操作，返回一个表格结果集，其中包含操作名称、查询字符串、所查询的索引以及找到的文档数。 最后两个语句排除包含空或未指定搜索的查询字符串（通过示例索引），这会在结果中削减干扰。

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2019-05-06&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. （可选）在*Query_s*上设置列筛选器，以搜索特定的语法或字符串。 例如，可以将筛选器*等于*`?api-version=2019-05-06&search=*&%24filter=HotelName`）。

   ![记录的查询字符串](./media/search-monitor-usage/log-query-strings.png "记录的查询字符串")

尽管此方法适用于即席调查，但生成报表后，你可以将查询字符串合并并呈现在布局中，以便进行更有利于的分析。

## <a name="identify-long-running-queries"></a>标识长时间运行的查询

添加 "持续时间" 列以获取所有查询的数量，而不仅仅是作为指标选取的所有查询。 对此数据进行排序时，会显示哪些查询的完成时间最长。

1. 在 "监视" 部分下，选择 "**日志**" 以查询日志信息。

1. 运行以下查询以返回按持续时间（以毫秒为单位）排序的查询。 最长运行的查询位于顶部。

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![按持续时间对查询进行排序](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "按持续时间对查询进行排序")

## <a name="create-a-metric-alert"></a>创建指标警报

指标警报建立阈值，您将收到一个通知或触发您预先定义的纠正措施。 

对于搜索服务，通常会创建用于搜索延迟和限制查询的指标警报。 如果你知道删除查询的时间，则可以查看降低负载或增加容量的补救措施。 例如，如果在索引期间限制查询增加，则可以将其推迟到 query activity 下降。

推送特定副本分区配置的限制时，为查询卷阈值（QPS）设置警报也很有用。

1. 在 "监视" 部分下，选择 "**警报**"，然后单击 " **+ 新建警报规则**"。 请确保选择搜索服务作为资源。

1. 在 "条件" 下，单击 "**添加**"。

1. 配置信号逻辑。 对于 "信号类型"，请选择 "**指标**"，然后选择信号。

1. 选择信号后，可以使用图表直观显示历史数据，以获得有关如何继续设置条件的明智决策。

1. 接下来，向下滚动到 "警报逻辑"。 对于概念证明，可以为测试目的指定人为低值。

   ![警报逻辑](./media/search-monitor-usage/alert-logic-qps.png "警报逻辑")

1. 接下来，指定或创建操作组。 这是在达到阈值时要调用的响应。 它可能是推送通知或自动响应。

1. 最后，指定警报详细信息。 命名并描述警报，分配严重性值，并指定是否在启用或禁用状态下创建规则。

   ![警报详细信息](./media/search-monitor-usage/alert-details.png "警报详细信息")

如果你指定了电子邮件通知，你将收到来自 "Microsoft Azure" 的电子邮件，其中主题行为 "Azure：已激活严重性： 3 `<your rule name>`"。

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>后续步骤

如果尚未这样做，请查看搜索服务监视基础知识，了解全面的监督功能。

> [!div class="nextstepaction"]
> [监视 Azure 中的操作和活动认知搜索](search-monitor-usage.md)