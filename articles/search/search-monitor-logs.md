---
title: 收集日志数据
titleSuffix: Azure Cognitive Search
description: 通过启用诊断设置来收集和分析日志数据，然后使用 Kusto 查询语言浏览结果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462340"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>收集和分析 Azure 认知搜索的日志数据

诊断或操作日志可深入了解 Azure 认知搜索的详细操作，并可用于监视服务和工作负荷进程。 在内部，日志会短时存在于后端，但足以进行调查和分析（如果你提交了支持票证）。 但是，如果你想要在操作数据上使用自助，则应配置诊断设置以指定收集日志记录信息的位置。

设置日志适用于诊断和保留操作历史记录。 启用日志记录后，可以运行查询或生成用于结构化分析的报表。

下表列举了用于收集和保存数据的选项。

| 资源 | 用于 |
|----------|----------|
| [发送到 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | 事件和度量值将发送到 Log Analytics 工作区，可在门户中查询该工作区以返回详细信息。 有关简介，请参阅[Azure Monitor 日志入门](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [将 Blob 存储存档](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | 事件和指标存档到 Blob 容器并存储在 JSON 文件中。 日志可以精确到（每小时/每分钟），对于研究特定的事件，而不是针对开放式调查非常有用。 使用 JSON 编辑器可以查看原始日志文件，也可以使用 Power BI 来聚合和可视化日志数据。|
| [流式传输到事件中心](https://docs.microsoft.com/azure/event-hubs/) | 将事件和指标流式传输到 Azure 事件中心服务。 对于很大的日志，请选择此项作为备用数据收集服务。 |

Azure Monitor 日志和 Blob 存储都作为免费服务提供，因此你可以免费试用 Azure 订阅的生存期。 Application Insights 可以免费注册和使用，前提是应用程序数据大小不超出特定限制（有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/monitor/)）。

## <a name="prerequisites"></a>必备条件

如果使用 Log Analytics 或 Azure 存储空间，则可以提前创建资源。

+ [创建 log analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>启用数据收集

诊断设置指定如何收集记录的事件和指标。

1. 在“监视”下，选择“诊断设置”。

   ![诊断设置](./media/search-monitor-usage/diagnostic-settings.png "诊断设置")

1. 选择 " **+ 添加诊断设置**"

1. 选中 " **Log Analytics**"，选择工作区，并选择 " **insight-logs-operationlogs** " 和 " **AllMetrics**"。

   ![配置数据收集](./media/search-monitor-usage/configure-storage.png "配置数据收集")

1. 保存设置。

1. 启用日志记录后，请使用搜索服务开始生成日志和指标。 记录的事件和度量值变得可用之前需要花费一段时间。

对于 Log Analytics，在数据可用之前的几分钟内，你可以运行 Kusto 查询来返回数据。 有关详细信息，请参阅[监视查询请求](search-monitor-logs.md)。

对于 Blob 存储，在 Blob 存储中出现容器前需要一小时。 每个容器每小时会有一个 blob。 仅当存在要记录或度量的活动时，才会创建容器。 将数据复制到存储帐户时，数据会被格式化为 JSON 并置于两个容器中：

+ insights-logs-operationlogs：用于搜索流量日志
+ insights-metrics-pt1m：用于指标

## <a name="query-log-information"></a>查询日志信息

在诊断日志中，两个表包含 Azure 认知搜索的日志和指标： **AzureDiagnostics**和**AzureMetrics**。

1. 在 "**监视**" 下，选择 "**日志**"。

1. 在查询窗口中，输入**AzureMetrics** 。 运行此简单查询以熟悉此表中收集的数据。 在表中滚动以查看度量值和值。 请注意顶部的记录计数，如果你的服务已收集一段时间的指标，你可能需要调整时间间隔以获取可管理的数据集。

   ![AzureMetrics 表](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics 表")

1. 输入以下查询返回表格结果集。

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. 重复前面的步骤，从**AzureDiagnostics**开始返回所有列以提供信息，然后再执行更具选择性的查询来提取更有趣的信息。

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics 表](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics 表")

## <a name="log-schema"></a>日志架构

包含 Azure 认知搜索日志数据的数据结构符合下面的架构。 

对于 Blob 存储，每个 blob 都有一个名为**记录**的根对象，其中包含一组日志对象。 每个 Blob 包含同一小时内发生的所有操作的记录。

下表是诊断日志记录常见的部分字段列表。

| 名称 | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| TimeGenerated |datetime |"2018-12-07T00:00:43.6872559Z" |操作的时间戳 |
| resourceId |字符串 |“/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE” |ResourceId |
| operationName |字符串 |“Query.Search” |操作的名称 |
| operationVersion |字符串 |"2019-05-06" |使用的 api-version |
| category |字符串 |“OperationLogs” |常量 |
| resultType |字符串 |“Success” |可能的值：成功或失败 |
| resultSignature |int |200 |HTTP 结果代码 |
| durationMS |int |50 |操作持续时间，以毫秒为单位 |
| properties |对象 (object) |请参阅下表 |包含特定于操作的数据的对象 |

### <a name="properties-schema"></a>属性架构

以下属性特定于 Azure 认知搜索。

| 名称 | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| Description_s |字符串 |“GET /indexes('content')/docs” |操作的终结点 |
| Documents_d |int |42 |处理的文档数目 |
| IndexName_s |字符串 |"测试索引" |与操作关联的索引名称 |
| Query_s |字符串 |"?search=AzureSearch&$count=true&api-version=2019-05-06" |查询参数 |

## <a name="metrics-schema"></a>度量值架构

为查询请求捕获指标，并按一分钟间隔进行度量。 每个度量值都会显示每分钟的最小、最大和平均值。 有关详细信息，请参阅[监视查询请求](search-monitor-queries.md)。

| 名称 | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| resourceId |字符串 |“/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE” |你的资源 ID |
| metricName |字符串 |“Latency” |度量值名称 |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |操作的时间戳 |
| average |int |64 |指标时间间隔内原始样本的平均值（以秒为单位）或百分比，取决于指标。 |
| minimum |int |37 |指标时间间隔内原始样本的最小值，单位为秒。 |
| maximum |int |78 |指标时间间隔内原始样本的最大值，单位为秒。  |
| total |int |258 |度量值时间间隔内原始样本的总值（以秒为单位）。  |
| count |int |4 |一分钟时间间隔内从节点发送到日志的指标数。  |
| timegrain |字符串 |“PT1M” |ISO 8601 中指标的时间粒度。 |

查询通常以毫秒为单位执行，因此仅以秒为单位的查询会显示在 QPS 中。

对于**每秒搜索查询**指标，最小值是在该分钟内注册的每秒搜索查询的最小值。 最大值也是如此。 平均值是一分钟内的聚合值。 例如，在一分钟内，你可能具有如下所示的模式： SearchQueriesPerSecond 的最大值的1秒，后跟平均负载58秒，最后一秒只包含一个查询，这是最小值。

对于受**限制的搜索查询百分比**、最小值、最大值、平均值和总计值，所有值都具有相同的值：已限制的搜索查询百分比，从搜索查询总数到一分钟。

## <a name="view-raw-log-files"></a>查看原始日志文件

Blob 存储用于存档日志文件。 可以使用任何 JSON 编辑器来查看日志文件。 如果没有编辑器，建议使用 [Visual Studio Code](https://code.visualstudio.com/download)。

1. 在 Azure 门户中打开存储帐户。 

2. 在左侧导航窗格中，单击“Blob”。 此时会看到 **insights-logs-operationlogs** 和 **insights-metrics-pt1m**。 这些容器由 Azure 认知搜索在将日志数据导出到 Blob 存储时创建。

3. 单击文件夹层次结构，直至找到 .json 文件。  通过上下文菜单来下载文件。

待文件下载完以后，在 JSON 编辑器中将其打开即可查看内容。

## <a name="next-steps"></a>后续步骤

如果尚未这样做，请查看搜索服务监视基础知识，了解全面的监督功能。

> [!div class="nextstepaction"]
> [监视 Azure 中的操作和活动认知搜索](search-monitor-usage.md)