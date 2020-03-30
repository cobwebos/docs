---
title: 监控操作和活动
titleSuffix: Azure Cognitive Search
description: 启用日志记录，从 Azure 认知搜索服务获取查询活动指标、资源使用情况以及其他系统数据。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462320"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>监视 Azure 认知搜索的操作和活动

本文介绍了在工作负载级别（查询和索引）的服务（资源）级别的监视，并建议一个监视用户访问的框架。

在一个范围内，您将使用内置基础结构和基础服务（如 Azure 监视器）的组合，以及返回统计信息、计数和状态的服务 API 的组合。 了解功能范围可以帮助您构建反馈循环，以便在问题出现时解决问题。

## <a name="use-azure-monitor"></a>使用 Azure Monitor

许多服务（包括 Azure 认知搜索）都利用[Azure 监视器](https://docs.microsoft.com/azure/azure-monitor/)来查找警报、指标和记录诊断数据。 对于 Azure 认知搜索，内置监视基础结构主要用于资源级监视（服务运行状况）和[查询监视](search-monitor-queries.md)。

以下屏幕截图可帮助您在门户中查找 Azure 监视器功能。

+ **监视**选项卡位于主概述页中，一目了然地显示关键指标。
+ **活动日志**（概述）下方报告资源级操作：服务运行状况和 API 密钥请求通知。
+ **监视**（在列表的进一步向下）提供可配置的警报、指标和诊断日志。 在需要时创建这些。 收集和存储数据后，您可以查询或可视化信息以获取见解。

![Azure 监视器在搜索服务中的集成](./media/search-monitor-usage/azure-monitor-search.png
 "Azure 监视器在搜索服务中的集成")

### <a name="precision-of-reported-numbers"></a>报告数字的精度

门户页面每隔几分钟刷新一次。 因此，门户中报告的数字是近似值，旨在让您大致了解系统对请求的服务情况。 实际指标（如每秒查询次数 （QPS） 可能高于或低于页面上显示的数字。

## <a name="activity-logs-and-service-health"></a>活动日志和服务运行状况

[**活动日志**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)从 Azure 资源管理器收集信息，并报告对服务运行状况的更改。 您可以监视活动日志中有关与服务运行状况相关的关键、错误和警告条件。

对于服务内任务（如查询、索引或创建对象），您将看到一些通用的信息通知，如为每个请求*获取管理密钥*和*获取查询密钥*，但看不到特定操作本身。 有关此颗粒的信息，必须配置诊断日志记录。

可以通过左侧导航窗格、顶部窗口命令栏中的“通知”或者“诊断并解决问题”页访问**活动日志**。****

## <a name="monitor-storage"></a>监控存储

"概述"页中内置的选项卡式页面报告资源使用情况。 一旦开始使用服务，此信息就会可用，无需配置，并且页面每隔几分钟刷新一次。 

对于[将哪个层用于生产工作负荷](search-sku-tier.md)或是否要[调整活动副本和分区的数目](search-capacity-planning.md)这样的问题，可以根据这些指标进行最终决策，因为这些指标会显示资源的消耗速度，以及当前配置处理现有负载的有效程度。

与存储相关的警报当前不可用;存储消耗不会聚合或登录到 Azure 监视器中的**Azure Metrics**表。 您需要[构建一个自定义解决方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating)来发出与资源相关的通知，其中代码检查存储大小并处理响应。 有关存储指标的详细信息，请参阅[获取服务统计信息](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response)。

对于门户中的可视监视，"**使用情况"** 选项卡显示资源可用性相对于服务层施加的当前[限制](search-limits-quotas-capacity.md)。 

下图描述免费服务的情况，该服务的上限是每个类型 3 个对象，最大存储为 50 MB。 “基本”或“标准”服务的限制更高，在增加分区计数的情况下，最大存储会按比例增大。

![相对于层限制的使用状态](./media/search-monitor-usage/usage-tab.png
 "相对于层限制的使用状态")

## <a name="monitor-workloads"></a>监控工作负载

记录的事件包括与索引和查询相关的事件。 日志分析中的**Azure 诊断**表收集与查询和索引相关的操作数据。

大多数记录的数据用于只读操作。 对于日志中未捕获的其他创建-更新-删除操作，可以查询搜索服务以查找系统信息。

| OperationName | 描述 |
|---------------|-------------|
| 服务统计 | 此操作是获取[服务统计信息](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)的常规调用，直接调用或隐式调用以在加载或刷新门户概览页时填充该页。 |
| 查询.搜索 |  查询针对索引的请求，请参阅[监视器查询](search-monitor-queries.md)，了解有关记录查询的信息。|
| 索引.索引  | 此操作是添加[、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)的调用。 |
| 指标。原型 | 这是由导入数据向导创建的索引。 |
| 索引器.创建 | 通过"导入数据"向导显式或隐式创建索引器。 |
| 索引器.获取 | 每当运行索引器时，都会返回索引器的名称。 |
| 索引器.状态 | 每当运行索引器时，返回索引器的状态。 |
| 数据源.获取 | 每当运行索引器时，都会返回数据源的名称。|
| 索引.获取 | 每当运行索引器时返回索引的名称。 |

### <a name="kusto-queries-about-workloads"></a>关于工作负载的库斯托查询

如果启用了日志记录，则可以查询**Azure 诊断**，以查询在服务上运行的操作列表以及何时运行的操作。 您还可以关联活动以调查性能变化。

#### <a name="example-list-operations"></a>示例：列出操作 

返回操作列表和每个操作的计数。

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>示例：关联操作

将查询请求与索引操作关联，并在时间图表上呈现数据点以查看操作一致。

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

## <a name="monitor-user-access"></a>监视用户访问

由于搜索索引是大型客户端应用程序的组件，因此没有用于控制或监视每个用户访问索引的内置方法。 假定请求来自客户端应用程序，用于管理员或查询请求。 管理员读写操作包括在整个服务中创建、更新和删除对象。 只读操作是针对文档集合的查询，范围为单个索引。 

因此，您将在活动日志中看到的是使用管理密钥或查询密钥调用的引用。 来自客户端代码的请求中包含相应的密钥。 该服务不具备处理标识令牌或模拟的设备。

当每个用户授权的业务需求确实存在时，建议与 Azure 活动目录集成。 您可以使用$filter和用户标识[来修剪](search-security-trimming-for-azure-search-with-aad.md)用户不应看到的文档的搜索结果。 

无法将此信息与包含$filter参数的查询字符串分开记录。 有关报告查询字符串的详细信息[，请参阅监视器查询](search-monitor-queries.md)。

## <a name="next-steps"></a>后续步骤

使用 Azure 监视器的流畅性对于监督任何 Azure 服务（包括 Azure 认知搜索等资源）至关重要。 如果您不熟悉 Azure 监视器，请花时间查看与资源相关的文章。 除了教程之外，以下文章是一个很好的开始位置。

> [!div class="nextstepaction"]
> [使用 Azure 监视器监视 Azure 资源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
