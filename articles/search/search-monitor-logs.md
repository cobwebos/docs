---
title: 收集日志数据
titleSuffix: Azure Cognitive Search
description: 通过启用诊断设置来收集和分析日志数据，然后使用 Kusto 查询语言浏览结果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 52230d6b13c4210e0ff8e85d0a3efe39af55f6e2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935052"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>收集和分析 Azure 认知搜索的日志数据

诊断或操作日志提供 Azure 认知搜索的详细操作的见解，可用于监视服务和工作负荷流程。 在内部，某些系统信息会短时存在于后端，但足以进行调查和分析（如果你提交了支持票证）。 但是，如果想要自我掌控操作数据，则应配置诊断设置以指定要从何处收集日志记录信息。

诊断日志记录功能是通过与 [Azure Monitor](../azure-monitor/index.yml) 集成实现的。 

设置诊断日志记录时，系统将要求你指定存储机制。 下表列举了用于收集和保留数据的选项。

| 资源 | 用途 |
|----------|----------|
| [发送到 Log Analytics 工作区](../azure-monitor/learn/tutorial-resource-logs.md) | 事件和指标将发送到 Log Analytics 工作区，可在门户中查询该工作区以返回详细信息。 有关介绍，请参阅 [Azure Monitor 日志入门](../azure-monitor/log-query/get-started-portal.md) |
| [使用 Blob 存储进行存档](../storage/blobs/storage-blobs-overview.md) | 事件和指标将存档到 Blob 容器，并存储在 JSON 文件中。 日志可以精确到小时/分钟，对于调查特定的事件非常有用，但不适合用于无目标性的调查。 使用 JSON 编辑器查看原始日志文件，或使用 Power BI 来聚合与可视化日志数据。|
| [流式传输到事件中心](../event-hubs/index.yml) | 事件和指标将流式传输到 Azure 事件中心服务。 对于很大的日志，请选择此项作为备用数据收集服务。 |

## <a name="prerequisites"></a>先决条件

提前创建资源，以便在配置诊断日志记录时可以选择一个或多个资源。

+ [创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)

+ [创建存储帐户](../storage/common/storage-account-create.md)

+ [创建事件中心](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>启用数据收集

诊断设置指定如何收集记录的事件和指标。

1. 在“监视”下，选择“诊断设置” 。

   ![诊断设置](./media/search-monitor-usage/diagnostic-settings.png "诊断设置")

1. 选择“+ 添加诊断设置”

1. 选中“Log Analytics”，选择你的工作区，然后选择“OperationLogs”和“AllMetrics”。  

   ![配置数据收集](./media/search-monitor-usage/configure-storage.png "配置数据收集")

1. 保存设置。

1. 启用日志记录后，使用搜索服务开始生成日志和指标。 记录的事件和指标需在一段时间后才可供使用。

对于 Log Analytics，数据将在几分钟后可供使用，然后可以运行 Kusto 查询来返回数据。 有关详细信息，请参阅[监视查询请求](search-monitor-logs.md)。

对于 Blob 存储，容器将在一小时后出现在 Blob 存储中。 每个容器每小时会有一个 blob。 仅当存在要记录或度量的活动时，才会创建容器。 将数据复制到存储帐户时，数据会被格式化为 JSON 并置于两个容器中：

+ insights-logs-operationlogs：用于搜索流量日志
+ insights-metrics-pt1m：用于指标

## <a name="query-log-information"></a>查询日志信息

两个表包含 Azure 认知搜索的日志和指标：**AzureDiagnostics** 和 **AzureMetrics**。

1. 在“监视”下选择“日志”。 

1. 在查询窗口中输入 **AzureMetrics**。 请运行此简单查询来熟悉此表中收集的数据。 滚动浏览整个表以查看指标和值。 请注意顶部的记录计数。如果服务已收集了一段时间的指标，你可以调整时间间隔以获取可管理的数据集。

   ![AzureMetrics 表](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics 表")

1. 输入以下查询以返回表格式结果集。

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. 从 **AzureDiagnostics** 开始重复前面的步骤，以返回所有列供参考，然后运行一个更有选择性的查询来提取更有意义的信息。

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics 表](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics 表")

## <a name="kusto-query-examples"></a>Kusto 查询示例

如果启用了诊断日志记录，可以查询 AzureDiagnostics，以获取在服务中运行的操作的列表及其运行时间。 还可以关联活动来调查性能的变化。

#### <a name="example-list-operations"></a>示例：列出操作 

返回操作的列表以及每个操作的计数。

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>示例：关联操作

将查询请求关联到索引编制操作，并在时间图表中呈现数据点，以确定操作是否一致。

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>记录的操作

Azure Monitor 捕获的记录事件包括与索引编制和查询相关的事件。 Log Analytics 中的 **AzureDiagnostics** 表收集与查询和索引编制相关的操作数据。

| OperationName | 说明 |
|---------------|-------------|
| ServiceStats | 此操作是对[获取服务统计信息](/rest/api/searchservice/get-service-statistics)的例行调用（直接调用或隐式调用），以便在加载或刷新门户概述页时在其中填充信息。 |
| Query.Search |  针对索引的查询请求。有关记录的查询的信息，请参阅[监视查询](search-monitor-queries.md)。|
| Indexing.Index  | 此操作是对[添加、更新或删除文档](/rest/api/searchservice/addupdate-or-delete-documents)的调用。 |
| indexes.Prototype | 这是导入数据向导创建的索引。 |
| Indexers.Create | 通过导入数据向导显式或隐式创建索引器。 |
| Indexers.Get | 每当运行索引器，就会返回该索引器的名称。 |
| Indexers.Status | 每当运行索引器，就会返回该索引器的状态。 |
| DataSources.Get | 每当运行索引器，就会返回数据源的名称。|
| Indexes.Get | 每当运行索引器，就会返回索引的名称。 |

## <a name="log-schema"></a>日志架构

如果要生成自定义报表，则包含 Azure 认知搜索日志数据的数据结构遵从以下架构。 对于 Blob 存储，每个 Blob 都有一个名为 **records** 的根对象，其中包含一系列日志对象。 每个 Blob 包含同一小时内发生的所有操作的记录。

下表是资源日志记录常用字段的部分列表。

| 名称 | 类型 | 示例 | 注释 |
| --- | --- | --- | --- |
| timeGenerated |datetime |"2018-12-07T00:00:43.6872559Z" |操作的时间戳 |
| ResourceId |字符串 |“/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE” |ResourceId |
| operationName |string |“Query.Search” |操作的名称 |
| operationVersion |string |"2020-06-30" |使用的 api-version |
| category |string |“OperationLogs” |constant |
| resultType |string |“Success” |可能的值：Success 或 Failure |
| resultSignature |int |200 |HTTP 结果代码 |
| durationMS |int |50 |操作持续时间，以毫秒为单位 |
| properties |object |请参阅下表 |包含特定于操作的数据的对象 |

### <a name="properties-schema"></a>属性架构

以下属性特定于 Azure 认知搜索。

| 名称 | 类型 | 示例 | 注释 |
| --- | --- | --- | --- |
| Description_s |string |“GET /indexes('content')/docs” |操作的终结点 |
| Documents_d |int |42 |处理的文档数目 |
| IndexName_s |string |"test-index" |与操作关联的索引名称 |
| Query_s |string |"?search=AzureSearch&$count=true&api-version=2020-06-30" |查询参数 |

## <a name="metrics-schema"></a>度量值架构

按一分钟间隔捕获和度量查询请求的指标。 每个度量值都会显示每分钟的最小、最大和平均值。 有关详细信息，请参阅[监视查询请求](search-monitor-queries.md)。

| 名称 | 类型 | 示例 | 注释 |
| --- | --- | --- | --- |
| ResourceId |字符串 |“/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE” |资源 ID |
| metricName |string |“Latency” |度量值名称 |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |操作的时间戳 |
| average |int |64 |指标时间间隔内原始样本的平均值，单位为秒或百分比，具体取决于指标。 |
| 最小值 |int |37 |指标时间间隔内原始样本的最小值，单位为秒。 |
| 最大值 |int |78 |指标时间间隔内原始样本的最大值，单位为秒。  |
| total |int |258 |指标时间间隔内原始样本的总计值，单位为秒。  |
| count |int |4 |在一分钟间隔内从节点发出到日志的指标数。  |
| timegrain |string |“PT1M” |采用 ISO 8601 的指标时间粒度。 |

查询往往在若干毫秒内即可完成执行，因此，指标中仅显示以秒度量的查询，例如 QPS。

对于“每秒搜索查询数”指标，最小值是该分钟内已注册的每秒搜索查询次数最低值。 最大值也是如此。 平均值是一分钟内的聚合值。 例如，在一分钟内可能出现如下所述的模式：有 1 秒出现高负载（这是 SearchQueriesPerSecond 的最大值），紧接着有 58 秒的平均负载，最后 1 秒只有 1 个查询（这是最小值）。

对于“受限制的搜索查询百分比”、最小值、最大值、平均值和总计，全都具有相同的值：在一分钟内的搜索查询总数中，已限制搜索查询百分比。

## <a name="view-raw-log-files"></a>查看原始日志文件

Blob 存储用于存档日志文件。 可以使用任何 JSON 编辑器来查看日志文件。 如果没有编辑器，建议使用 [Visual Studio Code](https://code.visualstudio.com/download)。

1. 在 Azure 门户中打开存储帐户。 

2. 在左侧导航窗格中，单击“Blob”。 此时会看到 **insights-logs-operationlogs** 和 **insights-metrics-pt1m**。 这些容器是在将日志数据导出到 Blob 存储时由 Azure 认知搜索创建的。

3. 单击文件夹层次结构，直至找到 .json 文件。  通过上下文菜单来下载文件。

待文件下载完以后，在 JSON 编辑器中将其打开即可查看内容。

## <a name="next-steps"></a>后续步骤

如果尚未这样做，请查看搜索服务监视基础知识，以全方面地了解监督功能。

> [!div class="nextstepaction"]
> [监视 Azure 认知搜索中的操作和活动](search-monitor-usage.md)