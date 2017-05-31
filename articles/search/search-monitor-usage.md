---
title: "监视 Azure 搜索服务中的使用情况和统计信息 | Microsoft Docs"
description: "跟踪 Azure 搜索（在 Microsoft Azure 上托管的云搜索服务）的资源消耗和索引大小。"
services: search
documentationcenter: 
author: bernitorres
manager: jlembicz
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 05/01/2017
ms.author: betorres
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 16cb5a1e16a59200f0e731622398efcf24c3f777
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="monitoring-an-azure-search-service"></a>监视 Azure 搜索服务

Azure 搜索提供用于跟踪搜索服务使用情况和性能的各种资源。 它提供对 Power BI 上指标、日志、索引统计信息和扩展的监视功能的访问。 本文介绍如何启用不同的监视策略以及如何解释生成的数据。

## <a name="azure-search-metrics"></a>Azure 搜索指标
指标提供接近实时的搜索服务可见性，且可用于每个服务，无需任何其他设置。 通过这些指标，可跟踪服务性能长达 30 天。

Azure 搜索针对三种不同指标收集数据：

* 搜索延迟：搜索服务处理搜索查询所需的时间（每分钟汇总一次）。
* 每秒搜索查询数 (QPS)：每秒接收的搜索查询次数（每分钟汇总一次）。
* 限制的搜索查询百分比：已限制的搜索查询百分比（每分钟汇总一次）。

![QPS 活动屏幕截图][1]

### <a name="set-up-alerts"></a>设置警报
在指标详细信息页中，可配置警报，使其在某个指标超出定义的阈值时触发电子邮件通知或某个自动操作。

有关指标的详细信息，请查看有关 Azure Monitor 的完整文档。  

## <a name="how-to-track-resource-usage"></a>如何跟踪资源使用情况
跟踪索引和文档大小的增长有助于在达到已为服务建立的上限之前主动调整容量。 可在门户中或使用 REST API 以编程方式来执行此操作。

### <a name="using-the-portal"></a>使用门户

若要监视资源使用情况，请在[门户](https://portal.azure.com)中查看服务的计数和统计信息。

1. 登录[门户](https://portal.azure.com)。
2. 打开 Azure 搜索服务的服务仪表板。 可以在主页上找到服务磁贴，或者可以从跳转栏上的“浏览”来浏览到服务。

“使用情况”部分包含的指标告知你当前正在使用可用资源的哪些部分。 有关索引、文档和存储的每个服务限制的信息，请参阅[服务限制](search-limits-quotas-capacity.md)。

  ![“使用情况”磁贴][2]

> [!NOTE]
> 上面的屏幕截图对应免费服务，每个服务最多具有一个副本和一个分区，并且只能承载 3 个索引、10000 个文档或 50 MB 数据（以先发生的为准）。 在“基本”或“标准”层上创建的服务具有更大的服务限制。 有关如何选择层的更多信息，请参阅[选择层或 SKU](search-sku-tier.md)。
>
>

### <a name="using-the-rest-api"></a>使用 REST API
Azure 搜索 REST API 和 .NET SDK 支持采用编程方式访问服务指标。  如果要使用[索引器](https://msdn.microsoft.com/library/azure/dn946891.aspx)从 Azure SQL 数据库或 Azure Cosmos DB 加载索引，可使用一个额外的 API 获取所需数字。

* [获取索引统计信息](/rest/api/searchservice/get-index-statistics)
* [计数文档](/rest/api/searchservice/count-documents)
* [获取索引器状态](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>如何导出日志和指标

可为服务导出操作日志，为前一部分所述的指标导出原始数据。 通过操作日志，可了解服务的使用方式，以及将数据复制到存储帐户时可从 Power BI 使用该服务的方式。 为此，Azure 搜索提供一个监视 Power BI 内容包。


### <a name="enabling-monitoring"></a>启用监视
在 [Azure 门户](http://portal.azure.com)中的“启用监视”选项下打开 Azure 搜索服务。

选择想要导出的数据：日志和/或指标。 可将数据复制到存储帐户，将其发送到事件中心，或将其导出到 Log Analytics。

![如何在门户中启用监视][3]

若要使用 PowerShell 或 Azure CLI 来启用监视，请参阅[此处](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs)的文档。

### <a name="logs-and-metrics-schemas"></a>日志和指标架构
将数据复制到存储帐户时，数据被格式化为 JSON 且置于两个容器中：

* insights-logs-operationlogs：用于搜索流量日志
* insights-metrics-pt1m：用于指标

每个容器每小时会有一个 blob。

示例路径：`resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>日志架构
日志 blob 包含搜索服务流量日志。
每个 Blob 具有一个名为 **records** 的根对象，该对象包含一组日志对象。
每个 blob 会记录同一小时内发生的所有操作。

| Name | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| time |datetime |“2015-12-07T00:00:43.6872559Z” |操作的时间戳 |
| resourceId |字符串 |“/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE” |你的 ResourceId |
| operationName |字符串 |“Query.Search” |操作的名称 |
| operationVersion |字符串 |"2015-02-28" |使用的 api-version |
| category |字符串 |“OperationLogs” |常量 |
| resultType |字符串 |“Success” |可能的值：成功或失败 |
| resultSignature |int |200 |HTTP 结果代码 |
| durationMS |int |50 |操作持续时间，以毫秒为单位 |
| 属性 |对象 |请参阅下表 |包含特定于操作的数据的对象 |

**属性架构**
| Name | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| 说明 |字符串 |“GET /indexes('content')/docs” |操作的终结点 |
| 查询 |字符串 |"?search=AzureSearch&$count=true&api-version=2015-02-28" |查询参数 |
| 文档 |int |42 |处理的文档数目 |
| IndexName |字符串 |“testindex” |与操作关联的索引名称 |

#### <a name="metrics-schema"></a>度量值架构
| Name | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| resourceId |字符串 |“/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE” |你的资源 ID |
| metricName |字符串 |“Latency” |度量值名称 |
| time |datetime |“2015-12-07T00:00:43.6872559Z” |操作的时间戳 |
| average |int |64 |度量值时间间隔内原始样本的平均值 |
| minimum |int |37 |度量值时间间隔内原始样本的最小值 |
| maximum |int |78 |度量值时间间隔内原始样本的最大值 |
| total |int |258 |度量值时间间隔内原始样本的总计值 |
| count |int |4 |用于生成度量值的原始样本数 |
| timegrain |字符串 |“PT1M” |采用 ISO 8601 的度量值时间粒度 |

所有度量值会按一分钟的时间间隔报告。 每个度量值都会显示每分钟的最小、最大和平均值。

对于 SearchQueriesPerSecond 度量值，最小值是该分钟内已注册的每秒搜索查询次数最低值。 最大值也是如此。 平均值是一分钟内的聚合值。
假设一分钟内出现以下情形：有 1 秒出现高负载（这是 SearchQueriesPerSecond 的最大值），紧接着有 58 秒的平均负载，最后 1 秒只有 1 个查询（这是最小值）。

对于 ThrottledSearchQueriesPercentage、minimum、maximum、average 和 total，全都具有相同的值：在一分钟内的搜索查询总数中，已限制搜索查询百分比。

## <a name="analyzing-your-data-with-power-bi"></a>使用 Power BI 分析数据

建议使用 [Power BI](https://powerbi.microsoft.com) 浏览和可视化数据。 可轻松将其连接到 Azure 存储帐户，并快速开始分析数据。

Azure 搜索提供 [Power BI 内容包](https://app.powerbi.com/getdata/services/azure-search)，通过此包可使用预定义的图表和表格监视并了解搜索流量。 它包含一组 Power BI 报告，可自动连接到数据并提供关于搜索服务的可视洞察力。 有关详细信息，请参阅[内容包帮助页](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/)。

![用于 Azure 搜索的 Power BI 仪表板][4]

## <a name="next-steps"></a>后续步骤
请查看[扩展副本和分区](search-limits-quotas-capacity.md)获取有关以下内容的指南：如何平衡为现有服务分配的分区和副本。

请访问[管理 Microsoft Azure 上的搜索服务](search-manage.md)，获取有关服务管理的更多信息，或访问[性能和优化](search-performance-optimization.md)获取优化指南。

了解有关创建出色报告的详细信息。 有关详细信息，请参阅 [Power BI Desktop 入门](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png

