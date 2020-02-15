---
title: 收集日志数据
titleSuffix: Azure Cognitive Search
description: 通过启用诊断设置来收集和分析日志数据，然后使用 Kusto 查询语言浏览结果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 2849dc94f1c45dda3da09120adebba6e004eb96b
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211173"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>收集和分析 Azure 认知搜索的日志数据

诊断或操作日志可深入了解 Azure 认知搜索的详细操作，并可用于监视服务和工作负荷进程。 在内部，日志会短时存在于后端，但足以进行调查和分析（如果你提交了支持票证）。 但是，如果你想要在操作数据上使用自助，则应配置诊断设置以指定收集日志记录信息的位置。 

设置日志适用于诊断和保留操作历史记录。 启用日志记录后，可以运行查询或生成用于结构化分析的报表。

下表列举了用于收集和保存数据的选项。

| 资源 | 用于 |
|----------|----------|
| [发送到 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | 根据下面的架构记录事件和查询指标。 事件记录到 Log Analytics 工作区中。 使用 Log Analytics，可以运行查询以返回详细信息。 有关详细信息，请参阅[Azure Monitor 日志入门](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [将 Blob 存储存档](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | 根据下面的架构记录事件和查询指标。 事件记录到 Blob 容器并存储在 JSON 文件中。 日志可以精确到（每小时/每分钟），对于研究特定的事件，而不是针对开放式调查非常有用。 使用 JSON 编辑器查看日志文件。|
| [流式传输到事件中心](https://docs.microsoft.com/azure/event-hubs/) | 记录的事件和查询指标，基于本文中记录的架构。 对于很大的日志，请选择此项作为备用数据收集服务。 |

Azure Monitor 日志和 Blob 存储都作为免费服务提供，因此你可以免费试用 Azure 订阅的生存期。 Application Insights 可以免费注册和使用，前提是应用程序数据大小不超出特定限制（有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/monitor/)）。

## <a name="prerequisites"></a>必备条件

如果使用 Log Analytics 或 Azure 存储空间，则可以提前创建资源。

+ [创建 log analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ 如果需要日志存档，请[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。

## <a name="create-a-log"></a>创建日志

诊断设置定义数据收集。 设置指定收集方式和方式。 

1. 在“监视”下，选择“诊断设置”。

   ![诊断设置](./media/search-monitor-usage/diagnostic-settings.png "诊断设置")

1. 选择 " **+ 添加诊断设置**"

1. 选择想要导出的数据：日志和/或指标。 可以收集存储帐户、log analytics 工作区中的数据，或将其流式传输到事件中心。

   建议使用 Log analytics，因为你可以在门户中查询工作区。

   如果还使用 Blob 存储，则在导出日志数据时，将根据需要创建容器和 blob。

   ![配置数据收集](./media/search-monitor-usage/configure-storage.png "配置数据收集")

1. 保存设置。

1. 通过创建或删除对象来测试（创建日志事件）以及提交查询（生成指标）。 

在 Blob 存储中，仅当存在要记录或度量的活动时，才会创建容器。 将数据复制到存储帐户时，数据会被格式化为 JSON 并置于两个容器中：

* insights-logs-operationlogs：用于搜索流量日志
* insights-metrics-pt1m：用于指标

**在 Blob 存储中显示容器之前需要一小时。每个容器有一个 blob （每小时）。**

日志每小时进行一次存档，其中活动发生。 以下路径是在 12 2020 年1月1日上午9:00 创建的一个日志文件的示例。 其中每个 `/` 都是文件夹： `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2020/m=01/d=12/h=09/m=00/name=PT1H.json`

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

## <a name="view-log-files"></a>查看日志文件

Blob 存储用于存档日志文件。 可以使用任何 JSON 编辑器来查看日志文件。 如果没有编辑器，建议使用 [Visual Studio Code](https://code.visualstudio.com/download)。

1. 在 Azure 门户中打开存储帐户。 

2. 在左侧导航窗格中，单击“Blob”。 此时会看到 **insights-logs-operationlogs** 和 **insights-metrics-pt1m**。 这些容器由 Azure 认知搜索在将日志数据导出到 Blob 存储时创建。

3. 单击文件夹层次结构，直至找到 .json 文件。  通过上下文菜单来下载文件。

待文件下载完以后，在 JSON 编辑器中将其打开即可查看内容。

## <a name="next-steps"></a>后续步骤

如果尚未这样做，请查看搜索服务监视基础知识，了解全面的监督功能。

> [!div class="nextstepaction"]
> [监视 Azure 中的操作和活动认知搜索](search-monitor-usage.md)