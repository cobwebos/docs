---
title: 监视查询
titleSuffix: Azure Cognitive Search
description: 监视性能和吞吐量的查询指标。 收集和分析诊断日志中的查询字符串输入。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: a3a313ef9cd74ba901f5a6a2d82a18e3c21145dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462509"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>监视 Azure 认知搜索中的查询请求

本文介绍如何使用指标和诊断日志记录来衡量查询性能和卷。 它还解释了如何收集查询中使用的输入词 - 当您需要评估搜索语料库的效用和有效性时，必要的信息。

馈入指标的历史数据将保留 30 天。 要延长保留时间，或报告操作数据和查询字符串，请确保启用[一个诊断设置](search-monitor-logs.md)，该设置指定用于持久化记录的事件和指标的存储选项。

最大化数据测量完整性的条件包括：

+ 使用计费服务（在基本层或标准层创建的服务）。 免费服务由多个订阅者共享，随着负载的转移，它会带来一定程度的波动。

+ 如果可能，请使用单个副本和分区来创建包含的隔离环境。 如果使用多个副本，查询指标将跨多个节点求平均值，这可能会降低结果的精度。 同样，多个分区意味着数据被分割，如果索引也在进行，则某些分区可能具有不同的数据。 调整查询性能时，单个节点和分区为测试提供了更稳定的环境。

> [!Tip]
> 借助其他客户端代码和应用程序见解，您还可以捕获点击率数据，从而更深入地了解吸引应用程序用户兴趣的内容。 有关详细信息，请参阅[搜索流量分析](search-traffic-analytics.md)。

## <a name="query-volume-qps"></a>查询卷 （QPS）

卷测量为**每秒搜索查询**（QPS），这是一个内置指标，可以报告为在一分钟内执行的查询的平均值、计数值、最小值或最大值。 指标的一分钟间隔（时间粒 =PT1M）在系统中固定。

查询通常以毫秒为单位执行，因此只有以秒为单位的查询才会显示在指标中。

| 聚合类型 | 描述 |
|------------------|-------------|
| 平均值 | 发生查询执行时一分钟内的平均秒数。|
| Count | 在一分钟间隔内向日志发出的指标数。 |
| 最大值 | 每分钟注册每秒搜索查询次数最多的一次。 |
| 最小值 | 每分钟每秒注册的搜索查询数最低。  |
| SUM | 分钟内执行的所有查询的总和。  |

例如，在一分钟内，您可能有一个这样的模式：一秒的高负载，即搜索查询Per秒的最大负载，然后是 58 秒的平均负载，最后只有一秒的查询，这是最小值。

另一个示例：如果一个节点发出 100 个指标，其中每个指标的值为 40，则"Count"为 100，"Sum"为 4000，"平均值"为 40，"最大值"为 40。

## <a name="query-performance"></a>查询性能

在服务范围内，查询性能衡量为搜索延迟（查询完成所需的时间）和由于资源争用而丢弃的受限查询。

### <a name="search-latency"></a>搜索延迟

| 聚合类型 | 延迟 | 
|------------------|---------|
| 平均值 | 平均查询持续时间（以毫秒为单位）。 | 
| Count | 在一分钟间隔内向日志发出的指标数。 |
| 最大值 | 示例中运行时间最长的查询。 | 
| 最小值 | 示例中运行最短的查询。  | 
| 总计 | 在间隔（一分钟）内执行示例中所有查询的总执行时间。  |

请考虑以下**搜索延迟**指标示例：对 86 个查询进行了采样，平均持续时间为 23.26 毫秒。 最小 0 表示删除了一些查询。 运行时间最长的查询需要 1000 毫秒才能完成。 总执行时间是 2 秒。

![延迟聚合](./media/search-monitor-usage/metrics-latency.png "延迟聚合")

### <a name="throttled-queries"></a>限制查询

限制查询是指删除而不是进程的查询。 在大多数情况下，限制是运行服务的正常部分。  它不一定表明出了什么问题。

当当前处理的请求数超过可用资源时，将发生限制。 当副本从旋转或索引期间被带出时，您可能会看到受限制的请求增加。 查询和索引请求都由同一组资源处理。

该服务确定是否根据资源消耗删除请求。 跨内存、CPU 和磁盘 IO 消耗的资源百分比在一段时间内为平均值。 如果此百分比超过阈值，则所有对索引的请求将受到限制，直到请求量减少。 

根据您的客户端，可以通过以下方式指示被限制的请求：

+ 服务返回一个错误"您发送的请求太多。 请稍后再试。” 
+ 服务返回 503 错误代码，指示该服务当前不可用。 
+ 如果您使用的是门户（例如，搜索资源管理器），则查询将静默删除，您需要再次单击"搜索"。

要确认限制的查询，请使用**节流搜索查询**指标。 您可以在门户中浏览指标，也可以创建如本文所述的警报指标。 对于在采样间隔内丢弃的查询，请使用*Total*获取未执行的查询的百分比。

| 聚合类型 | 遏制 |
|------------------|-----------|
| 平均值 | 在间隔内丢弃的查询百分比。 |
| Count | 在一分钟间隔内向日志发出的指标数。 |
| 最大值 | 在间隔内丢弃的查询百分比。|
| 最小值 | 在间隔内丢弃的查询百分比。 |
| 总计 | 在间隔内丢弃的查询百分比。 |

对于**受限搜索查询百分比**、最小、最大、平均值和总计，所有值都具有相同的值：从一分钟内搜索查询总数中限制的搜索查询的百分比。

在下面的屏幕截图中，第一个数字是计数（或发送到日志的指标数）。 其他聚合显示在顶部或悬停在指标上时，包括平均值、最大值和总计。 在此示例中，未删除任何请求。

![限制聚合](./media/search-monitor-usage/metrics-throttle.png "限制聚合")

## <a name="explore-metrics-in-the-portal"></a>在门户中浏览指标

为了快速查看当前数字，服务概述页上的 **"监视"** 选项卡显示三个指标（**搜索延迟**、**每秒搜索查询（每个搜索单元）、****限制搜索查询百分比**）（以小时、天和周为单位的固定间隔），并选择更改聚合类型。

为了进行更深入的探索，从 **"监视"** 菜单打开指标资源管理器，以便您可以分层、放大和可视化数据以探索趋势或异常。 通过完成[有关创建指标图表的本教程](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer)，了解有关指标资源管理器的更多信息。

1. 在"监视"部分下，选择**指标**以打开指标资源管理器，其范围设置为搜索服务。

1. 在"指标"下，从下拉列表中选择一个，并查看首选类型的可用聚合列表。 聚合定义如何在每个时间间隔内对收集的数据值进行采样。

   ![QPS 指标的指标资源管理器](./media/search-monitor-usage/metrics-explorer-qps.png "QPS 指标的指标资源管理器")

1. 在右上角，设置时间间隔。

1. 选择可视化效果。 默认值为折线图。

1. 通过选择 **"添加指标"** 并选择不同的聚合，将其他聚合分层。

1. 放大折线图上感兴趣的区域。 将鼠标指针放在区域的开头位置，单击并按住鼠标左键，拖动到区域的另一侧，然后松开按钮。 图表将在该时间范围内放大。

## <a name="identify-strings-used-in-queries"></a>标识查询中使用的字符串

启用诊断日志记录时，系统将捕获**Azure 诊断**表中的查询请求。 作为先决条件，您必须已启用[诊断日志记录](search-monitor-logs.md)，指定日志分析工作区或其他存储选项。

1. 在"监视"部分下，选择 **"日志"** 以在日志分析中打开一个空查询窗口。

1. 运行以下表达式以搜索 Query.Search 操作，返回由操作名称、查询字符串、查询索引和找到的文档数组成的表格结果集。 最后两个语句在示例索引上排除由空搜索或未指定的搜索组成的查询字符串，这减少了结果中的杂色。

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2019-05-06&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. 或者，在*Query_s*上设置列筛选器以搜索特定的语法或字符串。 例如，可以筛选*为*`?api-version=2019-05-06&search=*&%24filter=HotelName`。

   ![记录查询字符串](./media/search-monitor-usage/log-query-strings.png "记录查询字符串")

虽然此技术适用于临时调查，但生成报表允许您在更有利于分析的布局中合并和呈现查询字符串。

## <a name="identify-long-running-queries"></a>识别长时间运行的查询

添加持续时间列以获取所有查询的数字，而不仅仅是作为指标选取的查询的数字。 对这些数据进行排序将显示哪些查询完成的时间最长。

1. 在"监视"部分下，选择 **"日志**"以查询日志信息。

1. 运行以下查询以返回按持续时间（以毫秒为单位）排序的查询。 运行时间最长的查询位于顶部。

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![按持续时间对查询进行排序](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "按持续时间对查询进行排序")

## <a name="create-a-metric-alert"></a>创建指标警报

指标警报建立一个阈值，您可以在其中收到通知或触发您事先定义的纠正措施。 

对于搜索服务，通常为搜索延迟和限制查询创建指标警报。 如果您知道何时丢弃查询，则可以查找减少负载或增加容量的补救措施。 例如，如果在索引期间限制查询增加，则可以将其推迟到查询活动消退。

在推送特定副本分区配置的限制时，为查询卷阈值 （QPS） 设置警报也很有帮助。

1. 在"监视"部分下，选择 **"警报"，** 然后单击 **"新警报规则**"。 请确保您的搜索服务已被选为资源。

1. 在"条件"下，单击"**添加**"。

1. 配置信号逻辑。 对于信号类型，选择**指标**，然后选择信号。

1. 选择信号后，您可以使用图表来可视化历史数据，以便就如何继续设置条件做出明智的决策。

1. 接下来，向下滚动到警报逻辑。 对于概念验证，您可以为测试目的指定人为的低值。

   ![警报逻辑](./media/search-monitor-usage/alert-logic-qps.png "警报逻辑")

1. 接下来，指定或创建操作组。 这是满足阈值时要调用的响应。 它可能是推送通知或自动响应。

1. 最后，指定警报详细信息。 命名并描述警报，分配严重性值，并指定是在启用还是禁用状态下创建规则。

   ![警报详细信息](./media/search-monitor-usage/alert-details.png "警报详细信息")

如果指定了电子邮件通知，您将收到来自"Microsoft Azure"的电子邮件，主题行为"Azure：激活严重性：3"。 `<your rule name>`

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>后续步骤

如果您尚未这样做，请查看搜索服务监视的基础知识，以了解各种监督功能。

> [!div class="nextstepaction"]
> [监视 Azure 认知搜索中的操作和活动](search-monitor-usage.md)