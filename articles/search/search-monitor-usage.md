---
title: 监视操作和活动
titleSuffix: Azure Cognitive Search
description: 启用日志记录，从 Azure 认知搜索服务获取查询活动指标、资源使用情况以及其他系统数据。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 421fddb819d4d396d3ab8890789e58ccb935cbc0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806805"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>监视 Azure 认知搜索的操作和活动

本文概述了 Azure 认知搜索的监视概念和工具。 若要进行整体监视，可以结合使用内置功能和加载项服务，如 Azure Monitor。

您可以完全跟踪以下内容：

* 服务：运行状况/可用性和对服务配置的更改。
* 存储：已使用并且可用，每个内容类型的计数相对于服务层允许的配额。
* 查询活动：卷、延迟和限制或删除的查询。 记录的查询请求需要[Azure Monitor](#add-azure-monitor)。
* 索引活动：需要 Azure Monitor 的[诊断日志记录](#add-azure-monitor)。

搜索服务不支持按用户身份验证，因此在日志中找不到标识信息。

## <a name="built-in-monitoring"></a>内置监视

内置监视是指由搜索服务记录的活动。 除了诊断外，此监视级别无需进行任何配置。

Azure 认知搜索在滚动30天的时间内维护内部数据，用于报告服务运行状况和查询指标，你可以在门户中或通过这些[REST api](#monitoring-apis)找到这些指标。

以下屏幕截图可帮助你在门户中查找监视信息。 一旦你开始使用服务，数据就会变得可用。 每隔几分钟刷新一次门户页面。

* "**监视**" 选项卡上的 "概述" 页上显示查询量、延迟以及服务是否处于压力下。
* 左侧导航窗格中的 "**活动日志**" 连接到 Azure 资源管理器。 活动日志报告资源管理器执行的操作：服务可用性和状态、对容量（副本和分区）的更改和与 API 密钥相关的活动。
* **监视**设置进一步降低，提供可配置的警报、指标和诊断日志。 在需要时创建它们。 收集和存储数据后，可以查询或可视化信息以获取见解。

![搜索服务中的 Azure Monitor 集成](./media/search-monitor-usage/azure-monitor-search.png
 "搜索服务中的 Azure Monitor 集成")

> [!NOTE]
> 由于每隔几分钟刷新一次门户页面，因此报告的数字为近似值，旨在提供系统服务请求的正常程度。 实际指标（例如每秒查询数（QPS））可能高于或低于页面上显示的数字。 如果要求精度，请考虑使用 Api。

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>Api 适用于监视

你可以使用以下 Api 检索在门户的 "监视和使用情况" 选项卡中找到的相同信息。

* [获取服务统计信息](/rest/api/searchservice/get-service-statistics)
* [获取索引统计信息](/rest/api/searchservice/get-index-statistics)
* [获取文档计数](/rest/api/searchservice/count-documents)
* [获取索引器状态](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>活动日志和服务运行状况

门户中的 "[**活动日志**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)" 页面从 Azure 资源管理器收集信息，并报告服务运行状况的更改。 可以监视与服务运行状况相关的严重、错误和警告条件的活动日志。

常见条目包括对 API 密钥的引用-一般信息通知，如*获取管理密钥*和*获取查询密钥*。 这些活动指示使用管理密钥（创建或删除对象）或查询密钥发出的请求，但不显示请求本身。 有关这种情况的详细信息，必须配置诊断日志记录。

可以通过左侧导航窗格、顶部窗口命令栏中的“通知”或者“诊断并解决问题”页访问**活动日志**。****

### <a name="monitor-storage-in-the-usage-tab"></a>监视使用情况选项卡中的存储

对于门户中的可视监视，"**使用情况**" 选项卡会显示相对于服务层强加的当前[限制](search-limits-quotas-capacity.md)的资源可用性。 对于[将哪个层用于生产工作负荷](search-sku-tier.md)或是否要[调整活动副本和分区的数目](search-capacity-planning.md)这样的问题，可以根据这些指标进行最终决策，因为这些指标会显示资源的消耗速度，以及当前配置处理现有负载的有效程度。

下图描述免费服务的情况，该服务的上限是每个类型 3 个对象，最大存储为 50 MB。 “基本”或“标准”服务的限制更高，在增加分区计数的情况下，最大存储会按比例增大。

![相对于层限制的使用状态](./media/search-monitor-usage/usage-tab.png
 "相对于层限制的使用状态")

> [!NOTE]
> 与存储相关的警报当前不可用;不会聚合存储消耗，也不会将其记录到 Azure Monitor 中的**AzureMetrics**表中。 若要获取存储警报，你需要[构建一个自定义解决方案](../azure-monitor/insights/solutions-creating.md)，用于发出与资源相关的通知，你的代码将检查存储大小并处理响应。

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Azure Monitor 的加载项监视

许多服务（包括 Azure 认知搜索）都与[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)集成，以实现其他警报、指标和日志记录诊断数据。 

如果要控制数据收集和存储，请为搜索服务[启用诊断日志记录](search-monitor-logs.md)。 Azure Monitor 捕获的记录事件存储在**AzureDiagnostics**表中，并包含与查询和索引相关的操作数据。

Azure Monitor 提供了几个存储选项，你的选择将决定你如何使用数据：

* 如果要可视化 Power BI 报表中的[日志数据](search-monitor-logs-powerbi.md)，请选择 "Azure Blob 存储"。
* 如果要通过 Kusto 查询浏览数据，请选择 "Log Analytics"。

Azure Monitor 具有其自己的计费结构，此部分中引用的诊断日志具有相关的成本。 有关详细信息，请参阅[Azure Monitor 中的使用情况和预估成本](../azure-monitor/platform/usage-estimated-costs.md)。

## <a name="monitor-user-access"></a>监视用户访问权限

由于搜索索引是更大的客户端应用程序的一个组件，因此没有用于控制或监视每个用户对索引的访问的内置方法。 假设请求是来自客户端应用程序，用于管理请求或查询请求。 管理员读写操作包括在整个服务中创建、更新、删除对象。 只读操作是针对文档集合的查询，其作用域为单个索引。 

因此，你将在活动日志中看到的内容是使用管理密钥或查询密钥的调用的引用。 在源自客户端代码的请求中包括相应的密钥。 服务未配备用于处理标识令牌或模拟的情况。

如果每个用户的授权存在业务要求，建议与 Azure Active Directory 集成。 您可以使用 $filter 和用户标识来剪裁用户不应看到的文档的[搜索结果](search-security-trimming-for-azure-search-with-aad.md)。 

无法将此信息与包含 $filter 参数的查询字符串分开记录。 有关报告查询字符串的详细信息，请参阅[监视器查询](search-monitor-queries.md)。

## <a name="next-steps"></a>后续步骤

熟练与 Azure Monitor 对于任何 Azure 服务（包括 Azure 认知搜索等资源）的监督至关重要。 如果你不熟悉 Azure Monitor，请花时间查看与资源相关的文章。 除了教程外，以下文章是一个不错的开端。

> [!div class="nextstepaction"]
> [使用 Azure Monitor 监视 Azure 资源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
