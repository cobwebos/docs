---
title: 收集日志数据
titleSuffix: Azure Cognitive Search
description: 通过启用诊断设置收集和分析日志数据，然后使用 Kusto 查询语言来探索结果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462340"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>收集和分析 Azure 认知搜索的日志数据

诊断或操作日志提供 Azure 认知搜索的详细操作的见解，并且可用于监视服务和工作负载过程。 在内部，日志会短时存在于后端，但足以进行调查和分析（如果你提交了支持票证）。 但是，如果要对操作数据进行自向，则应配置诊断设置以指定收集日志记录信息的位置。

设置日志对于诊断和保留操作历史记录非常有用。 启用日志记录后，可以运行查询或生成报表以进行结构化分析。

下表列举了用于收集和持久化数据的选项。

| 资源 | 用于 |
|----------|----------|
| [发送到日志分析工作区](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | 事件和指标将发送到日志分析工作区，可以在门户中查询该工作区以返回详细信息。 有关简介，请参阅[使用 Azure 监视器日志入门](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [使用 Blob 存储存档](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | 事件和指标将存档到 Blob 容器并存储在 JSON 文件中。 日志可以非常精细（按小时/分钟）进行，可用于研究特定事件，但不适用于开放式调查。 使用 JSON 编辑器查看原始日志文件或 Power BI 以聚合和可视化日志数据。|
| [流到事件中心](https://docs.microsoft.com/azure/event-hubs/) | 事件和指标将流式传输到 Azure 事件中心服务。 对于很大的日志，请选择此项作为备用数据收集服务。 |

Azure Monitor 日志和 Blob 存储均以免费服务的形式提供，让你可以在 Azure 订阅的生存期内免费试用它。 Application Insights 可以免费注册和使用，前提是应用程序数据大小不超出特定限制（有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/monitor/)）。

## <a name="prerequisites"></a>先决条件

如果使用日志分析或 Azure 存储，则可以提前创建资源。

+ [创建日志分析工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>启用数据收集

诊断设置指定如何收集记录的事件和指标。

1. 在“监视”下，选择“诊断设置”********。

   ![诊断设置](./media/search-monitor-usage/diagnostic-settings.png "诊断设置")

1. 选择 **+ 添加诊断设置**

1. 选中**日志分析**，选择工作区，然后选择 **"操作日志**"和 **"所有指标**"。

   ![配置数据收集](./media/search-monitor-usage/configure-storage.png "配置数据收集")

1. 保存设置。

1. 启用日志记录后，使用搜索服务开始生成日志和指标。 记录的事件和指标可用需要时间。

对于日志分析，数据可用需要几分钟时间，之后即可运行 Kusto 查询以返回数据。 有关详细信息，请参阅[监视器查询请求](search-monitor-logs.md)。

对于 Blob 存储，容器在 Blob 存储中显示需要一个小时。 每个容器每小时会有一个 blob。 仅当存在要记录或度量的活动时，才会创建容器。 将数据复制到存储帐户时，数据会被格式化为 JSON 并置于两个容器中：

+ insights-logs-operationlogs：用于搜索流量日志
+ insights-metrics-pt1m：用于指标

## <a name="query-log-information"></a>查询日志信息

在诊断日志中，两个表包含 Azure 认知搜索的日志和指标 **：Azure 诊断**和**AzureMetrics**。

1. 在 **"监视"** 下，选择 **"日志**"。

1. 在查询窗口中输入**Azure 指标**。 运行此简单查询以熟悉此表中收集的数据。 滚动表以查看指标和值。 请注意顶部的记录计数，如果您的服务已收集指标一段时间，您可能需要调整时间间隔以获得可管理的数据集。

   ![AzureMetrics 表](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics 表")

1. 输入以下查询以返回表格结果集。

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. 重复前面的步骤，从**Azure 诊断**开始返回所有列以用于信息目的，然后是更选择性的查询，以提取更有趣的信息。

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Azure 诊断表](./media/search-monitor-usage/azurediagnostics-table.png "Azure 诊断表")

## <a name="log-schema"></a>日志架构

包含 Azure 认知搜索日志数据的数据结构符合以下架构。 

对于 Blob 存储，每个 Blob 都有一个称为**记录**的根对象，其中包含日志对象数组。 每个 Blob 包含同一小时内发生的所有操作的记录。

下表是诊断日志记录常见的字段的部分列表。

| “属性” | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| 时间生成 |datetime |"2018-12-07T00:00:43.6872559Z" |操作的时间戳 |
| resourceId |字符串 |“/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE” |ResourceId |
| operationName |字符串 |“Query.Search” |操作的名称 |
| operationVersion |字符串 |“2019-05-06” |使用的 api-version |
| category |字符串 |“OperationLogs” |常量 |
| resultType |字符串 |“Success” |可能的值：成功或失败 |
| resultSignature |int |200 |HTTP 结果代码 |
| durationMS |int |50 |操作持续时间，以毫秒为单位 |
| properties |对象 (object) |请参阅下表 |包含特定于操作的数据的对象 |

### <a name="properties-schema"></a>属性架构

以下属性特定于 Azure 认知搜索。

| “属性” | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| Description_s |字符串 |“GET /indexes('content')/docs” |操作的终结点 |
| Documents_d |int |42 |处理的文档数目 |
| IndexName_s |字符串 |"测试索引" |与操作关联的索引名称 |
| Query_s |字符串 |“?search=AzureSearch&$count=true&api-version=2019-05-06” |查询参数 |

## <a name="metrics-schema"></a>度量值架构

捕获查询请求的指标，并每隔一分钟测量一次。 每个度量值都会显示每分钟的最小、最大和平均值。 有关详细信息，请参阅[监视器查询请求](search-monitor-queries.md)。

| “属性” | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| resourceId |字符串 |“/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE” |资源 ID |
| metricName |字符串 |“Latency” |度量值名称 |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |操作的时间戳 |
| average |int |64 |原始样本的平均值（以指标时间间隔为单位）（单位以秒为单位）或百分比，具体取决于指标。 |
| minimum |int |37 |在指标时间间隔中原始样本的最小值，单位（以秒为单位）。 |
| maximum |int |78 |在指标时间间隔中原始样本的最大值，单位（以秒为单位）。  |
| total |int |258 |在指标时间间隔中原始样本的总值，单位（以秒为单位）。  |
| count |int |4 |在一分钟间隔内从节点向日志发出的指标数。  |
| timegrain |字符串 |“PT1M” |ISO 8601 中指标的时间粒度。 |

查询通常以毫秒为单位执行，因此只有以秒为单位的查询才会显示在指标中，如 QPS。

对于 **"每秒搜索查询"** 指标，最小值是该分钟中每秒注册的搜索查询的最低值。 最大值也是如此。 平均值是一分钟内的聚合值。 例如，在一分钟内，您可能有一个这样的模式：一秒的高负载，即搜索查询Per秒的最大负载，然后是 58 秒的平均负载，最后只有一秒的查询，这是最小值。

对于**受限搜索查询百分比**、最小、最大、平均值和总计，所有值都具有相同的值：从一分钟内搜索查询总数中限制的搜索查询的百分比。

## <a name="view-raw-log-files"></a>查看原始日志文件

Blob 存储用于存档日志文件。 可以使用任何 JSON 编辑器来查看日志文件。 如果没有编辑器，建议使用 [Visual Studio Code](https://code.visualstudio.com/download)。

1. 在 Azure 门户中打开存储帐户。 

2. 在左侧导航窗格中，单击“Blob”****。 此时会看到 **insights-logs-operationlogs** 和 **insights-metrics-pt1m**。 这些容器是在将日志数据导出到 Blob 存储时由 Azure 认知搜索创建的。

3. 单击文件夹层次结构，直至找到 .json 文件。  通过上下文菜单来下载文件。

待文件下载完以后，在 JSON 编辑器中将其打开即可查看内容。

## <a name="next-steps"></a>后续步骤

如果您尚未这样做，请查看搜索服务监视的基础知识，以了解各种监督功能。

> [!div class="nextstepaction"]
> [监视 Azure 认知搜索中的操作和活动](search-monitor-usage.md)