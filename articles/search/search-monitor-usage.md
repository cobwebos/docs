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
ms.openlocfilehash: d47f6c20246e3210b58dbc9c802a11c866ae305e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935001"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>监视 Azure 认知搜索的操作和活动

本文概述了针对 Azure 认知搜索的监视概念和工具。 若要进行全面的监视，可以将内置功能和 Azure Monitor 等附加服务结合使用。

总的来说，你可以跟踪以下内容：

* 服务：运行状况/可用性和对服务配置的更改。
* 存储：已用和可用空间，包含每种内容类型相对于服务层允许的配额的计数。
* 查询活动：查询量、延迟和受限或已删除的查询。 记录的查询请求需要使用 [Azure Monitor](#add-azure-monitor)。
* 索引活动：需要使用 Azure Monitor 进行[诊断日志记录](#add-azure-monitor)。

搜索服务不支持按用户进行身份验证，因此在日志中找不到标识信息。

## <a name="built-in-monitoring"></a>内置监视

内置监视是指由搜索服务记录的活动。 除了诊断外，此监视级别无需进行任何配置。

Azure 认知搜索按照连续 30 天的计划维护内部数据，以便报告服务运行状况和查询指标，你可以在门户中或通过这些 [REST API](#monitoring-apis) 找到相关信息。

以下屏幕截图可帮助你在门户中找到监视信息。 开始使用服务后，数据便可供使用。 门户页面每隔几分钟刷新一次。

* 主概述页上的“监视”选项卡显示查询量、延迟以及服务是否面临压力。
* 左侧导航窗格中的“活动日志”已连接到 Azure 资源管理器。 活动日志报告资源管理器未执行的操作：服务可用性和状态、对容量（副本和分区）的更改以及与 API 密钥相关的活动。
* 下方的“监视”设置提供可配置的警报、指标和诊断日志。 可按需创建这些内容。 收集并存储数据后，可以查询或可视化信息以获取见解。

![搜索服务中的 Azure Monitor 集成](./media/search-monitor-usage/azure-monitor-search.png
 "搜索服务中的 Azure Monitor 集成")

> [!NOTE]
> 由于门户页面每隔几分钟便会刷新一次，门户中报告的数字是近似值，旨在让你大致了解系统为请求提供服务时的表现。 实际指标（例如每秒查询数 (QPS)）可能高于或低于页面上显示的数字。 如果要求精度，请考虑使用 API。

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>有助于进行监视的 API

可以使用以下 API 检索门户中的“监视”和“使用情况”选项卡中找到的相同信息。

* [获取服务统计信息](/rest/api/searchservice/get-service-statistics)
* [获取索引统计信息](/rest/api/searchservice/get-index-statistics)
* [获取文档计数](/rest/api/searchservice/count-documents)
* [获取索引器状态](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>活动日志和服务运行状况

门户中的“[活动日志](../azure-monitor/platform/activity-log.md#view-the-activity-log)”页从 Azure 资源管理器收集信息，并报告服务运行状况的更改。 可以监视活动日志来了解服务运行状况相关的严重、错误和警告状态。

常见条目包括对 API 密钥的引用 - 一般信息通知，如“获取管理密钥”和“获取查询密钥” 。 这些活动指示使用管理密钥（创建或删除对象）或查询密钥发出的请求，但不显示请求本身。 如需这种粒度的信息，必须配置诊断日志记录。

可以通过左侧导航窗格、顶部窗口命令栏中的“通知”或者“诊断并解决问题”页访问**活动日志**。

### <a name="monitor-storage-in-the-usage-tab"></a>在“使用情况”选项卡中监视存储

门户中的“使用情况”选项卡相对于服务层级施加的当前[限制](search-limits-quotas-capacity.md)显示资源可用性让你直观地进行监视。 对于[将哪个层用于生产工作负荷](search-sku-tier.md)或是否要[调整活动副本和分区的数目](search-capacity-planning.md)这样的问题，可以根据这些指标进行最终决策，因为这些指标会显示资源的消耗速度，以及当前配置处理现有负载的有效程度。

下图描述免费服务的情况，该服务的上限是每个类型 3 个对象，最大存储为 50 MB。 “基本”或“标准”服务的限制更高，在增加分区计数的情况下，最大存储会按比例增大。

![相对于层级限制的使用状态](./media/search-monitor-usage/usage-tab.png
 "相对于层级限制的使用状态")

> [!NOTE]
> 目前不提供存储相关的警报；存储消耗量不会聚合或记录到 Azure Monitor 的 **AzureMetrics** 表中。 要获取存储警告，需要[生成自定义解决方案](../azure-monitor/insights/solutions.md)，用于发出资源相关的通知，代码可在其中检查存储大小并处理响应。

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>使用 Azure Monitor 进行更多监视

许多服务（包括 Azure 认知搜索）通过与 [Azure Monitor](../azure-monitor/index.yml) 集成来提供更多警报、指标和日志记录诊断数据。 

若要控制数据收集和存储，请为搜索服务[启用诊断日志记录](search-monitor-logs.md)。 Azure Monitor 捕获的已记录事件存储在 **AzureDiagnostics** 表中，其中包含与查询和编制索引相关的操作数据。

Azure Monitor 提供多个存储选项，你的选择将决定你如何使用数据：

* 如果要可视化 Power BI 报表中的 [日志数据](search-monitor-logs-powerbi.md) ，请选择 "Azure Blob 存储"。
* 如果想要通过 Kusto 查询浏览数据，请选择“Log Analytics”。

Azure Monitor 具有其自己的计费结构，此部分中引用的诊断日志具有相关的成本。 有关详细信息，请参阅[在 Azure Monitor 中监视使用情况和预估成本](../azure-monitor/platform/usage-estimated-costs.md)。

## <a name="monitor-user-access"></a>监视用户的访问

由于搜索索引是较大客户端应用程序的一个组件，因此，没有任何内置方法可用于控制或监视每个用户对索引的访问。 对于管理请求或查询请求，假设请求来自客户端应用程序。 管理读写操作包括在整个服务中创建、更新和删除对象。 只读操作是针对文档集合运行的查询，范围限定为单个索引。 

因此，活动日志中显示的内容是对使用管理密钥或查询密钥的调用的引用。 相应的密钥包含在源自客户端代码的请求中。 服务中未配备用于处理标识令牌或模拟的功能。

确实存在按用户授权的业务要求时，建议与 Azure Active Directory 集成。 可以使用 $filter 和用户标识来[修整](search-security-trimming-for-azure-search-with-aad.md)用户不应看到的文档的搜索结果。 

无法单独从包含 $filter 参数的查询字符串记录此信息。 有关报告查询字符串的详细信息，请参阅[监视查询](search-monitor-queries.md)。

## <a name="next-steps"></a>后续步骤

熟练运用 Azure Monitor 是监督任何 Azure 服务（包括 Azure 认知搜索等资源）的关键所在。 如果你不熟悉 Azure Monitor，请花些时间阅读资源相关的文章。 除教程以外，以下文章也是不错的入门资源。

> [!div class="nextstepaction"]
> [使用 Azure Monitor 监视 Azure 资源](../azure-monitor/insights/monitor-azure-resource.md)