---
title: 监视操作和活动
titleSuffix: Azure Cognitive Search
description: 启用日志记录、从 Azure 认知搜索服务获取查询活动指标、资源使用情况和其他系统数据。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 5846e9516548032595c1ce072d1dae8dcce9d39e
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443595"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>监视 Azure 认知搜索的操作和活动

本文介绍了在服务（资源）级别的监视工作负荷级别（查询和索引），并建议用于监视用户访问的框架。

在各种领域，你将结合使用内置基础结构和基础服务（如 Azure Monitor）以及返回统计信息、计数和状态的服务 Api。 了解一系列功能有助于构造反馈循环，以便在出现问题时对其进行处理。

## <a name="use-azure-monitor"></a>使用 Azure Monitor

许多服务（包括 Azure 认知搜索）都利用警报、指标和日志记录诊断数据的[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) 。 对于 Azure 认知搜索，内置的监视基础结构主要用于资源级监视（服务运行状况）和[查询监视](search-monitor-queries.md)。

以下屏幕截图可帮助你定位门户中 Azure Monitor 的功能。

+ "**监视**" 选项卡（位于主 "概述" 页中）将一目了然地显示关键指标。
+ **活动日志**，只需概述以下资源级操作的报告：服务运行状况和 API 密钥请求通知。
+ **监视**在列表中进一步向下提供可配置的警报、指标和诊断日志。 在需要时创建它们。 收集和存储数据后，可以查询或可视化信息以获取见解。

![搜索服务中的 Azure Monitor 集成](./media/search-monitor-usage/azure-monitor-search.png
 "搜索服务中的 Azure Monitor 集成")

### <a name="precision-of-reported-numbers"></a>报告的数字的精度

每隔几分钟刷新一次门户页面。 因此，在门户中报告的数字是大致的，旨在提供系统服务请求的良好表现。 实际指标（例如每秒查询数（QPS））可能高于或低于页面上显示的数字。

## <a name="activity-logs-and-service-health"></a>活动日志和服务运行状况

[**活动日志**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)从 Azure 资源管理器收集信息，并报告服务运行状况更改。 可以监视与服务运行状况相关的严重、错误和警告条件的活动日志。

对于服务内任务（例如查询、编制索引或创建对象），你将看到一般信息性通知，如*获取管理密钥*和获取每个请求的*查询密钥*，而不是特定操作本身。 有关这种情况的详细信息，必须配置诊断日志记录。

可以通过左侧导航窗格、顶部窗口命令栏中的“通知”或者“诊断并解决问题”页访问**活动日志**。

## <a name="monitor-storage"></a>监视存储

"概述" 页中内置的选项卡式页面报告资源使用情况。 此信息将在你开始使用服务时立即可用，无需进行任何配置，并且每隔几分钟刷新一次页面。 

对于[将哪个层用于生产工作负荷](search-sku-tier.md)或是否要[调整活动副本和分区的数目](search-capacity-planning.md)这样的问题，可以根据这些指标进行最终决策，因为这些指标会显示资源的消耗速度，以及当前配置处理现有负载的有效程度。

与存储相关的警报当前不可用;不会聚合存储消耗，也不会将其记录到 Azure Monitor 中的**AzureMetrics**表中。 你需要构建一个自定义解决方案，用于发出与资源相关的通知，你的代码将检查存储大小并处理响应。 有关存储指标的详细信息，请参阅[获取服务统计](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response)信息。

对于门户中的可视监视，"**使用情况**" 选项卡会显示相对于服务层强加的当前[限制](search-limits-quotas-capacity.md)的资源可用性。 

下图描述免费服务的情况，该服务的上限是每个类型 3 个对象，最大存储为 50 MB。 “基本”或“标准”服务的限制更高，在增加分区计数的情况下，最大存储会按比例增大。

![相对于层限制的使用状态](./media/search-monitor-usage/usage-tab.png
 "相对于层限制的使用状态")

## <a name="monitor-workloads"></a>监视工作负荷

记录的事件包括与索引和查询相关的事件。 Log Analytics 中的**AzureDiagnostics**表收集与查询和索引相关的操作数据。

大多数记录的数据用于只读操作。 对于日志中未捕获的其他 "创建-更新-删除" 操作，你可以在搜索服务中查询系统信息。

| OperationName | 说明 |
|---------------|-------------|
| ServiceStats | 此操作是一种用于[获取服务统计信息](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)的例程调用，此调用可以直接调用，也可以在加载或刷新门户网站概述页时隐式填充。 |
| 查询。搜索 |  针对索引的查询请求的详细信息，请参阅[监视器查询](search-monitor-queries.md)。|
| 索引编制索引  | 此操作是对[添加、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)进行的调用。 |
| 全文.原型 | 这是 "导入数据" 向导创建的索引。 |
| 索引器。创建 | 通过 "导入数据" 向导显式或隐式创建索引器。 |
| 索引器。 Get | 每当运行索引器时，返回索引器的名称。 |
| 索引器。状态 | 每当运行索引器时，返回索引器的状态。 |
| 数据源。获取 | 在运行索引器时返回数据源的名称。|
| 索引。 Get | 在运行索引器时返回索引的名称。 |

### <a name="kusto-queries-about-workloads"></a>有关工作负载的 Kusto 查询

如果启用了日志记录，则可以查询**AzureDiagnostics** ，以获取服务上运行的操作的列表以及何时运行。 你还可以关联活动以调查性能变化。

#### <a name="example-list-operations"></a>示例：列出操作 

返回一个操作列表和每个操作的计数。

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>示例：关联操作

将查询请求与索引操作相关联，并跨时间图表呈现数据点，以查看操作是否一致。

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>使用搜索 Api

Azure 认知搜索 REST API 和 .NET SDK 都提供对服务指标、索引和索引器信息以及文档计数的编程访问。

+ [获取服务统计信息](/rest/api/searchservice/get-service-statistics)
+ [获取索引统计信息](/rest/api/searchservice/get-index-statistics)
+ [获取文档计数](/rest/api/searchservice/count-documents)
+ [获取索引器状态](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>监视用户访问权限

由于搜索索引是更大的客户端应用程序的一个组件，因此没有用于控制或监视每个用户对索引的访问的内置方法。 假设请求是来自客户端应用程序，用于管理请求或查询请求。 管理员读写操作包括在整个服务中创建、更新、删除对象。 只读操作是针对文档集合的查询，其作用域为单个索引。 

因此，你将在活动日志中看到的内容是使用管理密钥或查询密钥的调用的引用。 在源自客户端代码的请求中包括相应的密钥。 服务未配备用于处理标识令牌或模拟的情况。

如果每个用户的授权存在业务要求，建议与 Azure Active Directory 集成。 您可以使用 $filter 和用户标识来剪裁用户不应看到的文档的[搜索结果](search-security-trimming-for-azure-search-with-aad.md)。 

无法将此信息与包含 $filter 参数的查询字符串分开记录。 有关报告查询字符串的详细信息，请参阅[监视器查询](search-monitor-queries.md)。

## <a name="next-steps"></a>后续步骤

熟练与 Azure Monitor 对于任何 Azure 服务（包括 Azure 认知搜索等资源）的监督至关重要。 如果你不熟悉 Azure Monitor，请花时间查看与资源相关的文章。 除了教程外，以下文章是一个不错的开端。

> [!div class="nextstepaction"]
> [利用 Azure Monitor 监视 Azure 资源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
