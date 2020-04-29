---
title: 监视操作和活动
titleSuffix: Azure Cognitive Search
description: 启用日志记录，从 Azure 认知搜索服务获取查询活动指标、资源使用情况以及其他系统数据。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77462320"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>监视 Azure 认知搜索的操作和活动

本文介绍服务（资源）级别以及工作负荷级别（查询和索引）的监视，并推荐用于监视用户访问的框架。

在不同的领域，你会结合使用内置基础结构和 Azure Monitor 等基础服务，以及可返回统计信息、计数和状态的服务 API。 了解功能的范围有助于构建反馈循环，以便可以解决出现的问题。

## <a name="use-azure-monitor"></a>使用 Azure Monitor

许多服务（包括 Azure 认知搜索）利用 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) 来提供警报、指标和日志记录诊断数据。 对于 Azure 认知搜索，内置监视基础结构主要用于资源级监视（服务运行状况）和[查询监视](search-monitor-queries.md)。

以下屏幕截图可帮助你在门户中找到 Azure Monitor 功能。

+ 位于概述主页中的“监视”选项卡显示关键指标的概览。 
+ 紧靠在“概述”下面的“活动日志”报告资源级操作：服务运行状况和 API 密钥请求通知。 
+ 列表中接下来的“监视”提供可配置的警报、指标和诊断日志。  可按需创建这些内容。 收集并存储数据后，可以查询或可视化信息以获取见解。

![搜索服务中的 Azure Monitor 集成](./media/search-monitor-usage/azure-monitor-search.png
 "搜索服务中的 Azure Monitor 集成")

### <a name="precision-of-reported-numbers"></a>报告数字的精确度

门户页面每隔几分钟刷新一次。 因此，门户中报告的数字是近似值，旨在让你大致了解系统为请求提供服务时的表现。 实际指标（例如每秒查询数 (QPS)）可能高于或低于页面上显示的数字。

## <a name="activity-logs-and-service-health"></a>活动日志和服务运行状况

[**活动日志**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)从 Azure 资源管理器收集信息，并报告服务运行状况的变化。 可以监视活动日志来了解服务运行状况相关的严重、错误和警告状态。

对于服务中的任务（例如查询、编制索引或创建对象），你会看到每个请求的一般信息性通知（例如“获取管理密钥”和“获取查询密钥”），但看不到特定的操作本身。   如需这种粒度的信息，必须配置诊断日志记录。

可以通过左侧导航窗格、顶部窗口命令栏中的“通知”或者“诊断并解决问题”页访问**活动日志**。 

## <a name="monitor-storage"></a>监视存储

“概述”页中内置的选项卡式页面报告资源的使用情况。 只要开始使用服务，就立即会提供此信息，不需要进行配置。页面每隔几分钟刷新一次。 

对于[将哪个层用于生产工作负荷](search-sku-tier.md)或是否要[调整活动副本和分区的数目](search-capacity-planning.md)这样的问题，可以根据这些指标进行最终决策，因为这些指标会显示资源的消耗速度，以及当前配置处理现有负载的有效程度。

目前不提供存储相关的警报；存储消耗量不会聚合或记录到 Azure Monitor 的 **AzureMetrics** 表中。 需要[生成一个自定义解决方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating)用于发出资源相关的通知，代码可在其中检查存储大小并处理响应。 有关存储指标的详细信息，请参阅[获取服务统计信息](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response)。

门户中的“使用情况”选项卡相对于服务层级施加的当前[限制](search-limits-quotas-capacity.md)显示资源可用性让你直观地进行监视。  

下图描述免费服务的情况，该服务的上限是每个类型 3 个对象，最大存储为 50 MB。 “基本”或“标准”服务的限制更高，在增加分区计数的情况下，最大存储会按比例增大。

![相对于层级限制的使用状态](./media/search-monitor-usage/usage-tab.png
 "相对于层级限制的使用状态")

## <a name="monitor-workloads"></a>监视工作负荷

记录的事件包括与索引编制和查询相关的事件。 Log Analytics 中的 **AzureDiagnostics** 表收集与查询和索引编制相关的操作数据。

记录的大部分数据是只读操作的数据。 对于不在日志中捕获的其他创建-更新-删除操作，可以在搜索服务中查询系统信息。

| OperationName | 说明 |
|---------------|-------------|
| ServiceStats | 此操作是对[获取服务统计信息](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)的例行调用（直接调用或隐式调用），以便在加载或刷新门户概述页时在其中填充信息。 |
| Query.Search |  针对索引的查询请求。有关记录的查询的信息，请参阅[监视查询](search-monitor-queries.md)。|
| Indexing.Index  | 此操作是对[添加、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)的调用。 |
| indexes.Prototype | 这是导入数据向导创建的索引。 |
| Indexers.Create | 通过导入数据向导显式或隐式创建索引器。 |
| Indexers.Get | 每当运行索引器，就会返回该索引器的名称。 |
| Indexers.Status | 每当运行索引器，就会返回该索引器的状态。 |
| DataSources.Get | 每当运行索引器，就会返回数据源的名称。|
| Indexes.Get | 每当运行索引器，就会返回索引的名称。 |

### <a name="kusto-queries-about-workloads"></a>有关工作负荷的 Kusto 查询

如果启用了日志记录，可以查询 **AzureDiagnostics**，以获取在服务中运行的操作的列表及其运行时间。 还可以关联活动来调查性能的变化。

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

### <a name="use-search-apis"></a>使用搜索 API

Azure 认知搜索 REST API 和 .NET SDK 支持采用编程方式访问服务指标、索引和索引器信息，以及文档计数。

+ [获取服务统计信息](/rest/api/searchservice/get-service-statistics)
+ [获取索引统计信息](/rest/api/searchservice/get-index-statistics)
+ [获取文档计数](/rest/api/searchservice/count-documents)
+ [获取索引器状态](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>监视用户的访问

由于搜索索引是较大客户端应用程序的一个组件，因此，没有任何内置方法可用于控制或监视每个用户对索引的访问。 对于管理请求或查询请求，假设请求来自客户端应用程序。 管理读写操作包括在整个服务中创建、更新和删除对象。 只读操作是针对文档集合运行的查询，范围限定为单个索引。 

因此，活动日志中显示的内容是对使用管理密钥或查询密钥的调用的引用。 相应的密钥包含在源自客户端代码的请求中。 服务中未配备用于处理标识令牌或模拟的功能。

确实存在按用户授权的业务要求时，建议与 Azure Active Directory 集成。 可以使用 $filter 和用户标识来[修整](search-security-trimming-for-azure-search-with-aad.md)用户不应看到的文档的搜索结果。 

无法单独从包含 $filter 参数的查询字符串记录此信息。 有关报告查询字符串的详细信息，请参阅[监视查询](search-monitor-queries.md)。

## <a name="next-steps"></a>后续步骤

熟练运用 Azure Monitor 是监督任何 Azure 服务（包括 Azure 认知搜索等资源）的关键所在。 如果你不熟悉 Azure Monitor，请花些时间阅读资源相关的文章。 除教程以外，以下文章也是不错的入门资源。

> [!div class="nextstepaction"]
> [利用 Azure Monitor 监视 Azure 资源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
