---
title: Azure 媒体服务中的流式处理定位符 | Microsoft Docs
description: 本文介绍何为流式处理定位符以及 Azure 媒体服务如何使用这些定位符。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: cb34855fc9451679c885eebb0ef5a2fab0be8c57
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086839"
---
# <a name="streaming-locators"></a>流式处理定位符

若要为客户提供可用于播放编码的视频或音频文件的 URL，需要创建 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) 并构建流式处理 URL。 有关详细信息，请参阅[对文件进行流式处理](stream-files-dotnet-quickstart.md)。

## <a name="streaminglocator-definition"></a>StreamingLocator 定义

下表显示了 StreamingLocator 的属性并给出了它们的定义。

|名称|Description|
|---|---|
|id |资源的完全限定的资源 ID。|
|名称   |资源的名称。|
|properties.alternativeMediaId|此流式处理定位符的备选媒体 ID。|
|properties.assetName   |资产名称|
|properties.contentKeys |此流式处理定位符使用的 Contentkey。|
|properties.created |流式处理定位符的创建时间。|
|properties.defaultContentKeyPolicyName|此流式处理定位符使用的默认 ContentKeyPolicy 的名称。|
|properties.endTime |流式处理定位符的结束时间。|
|properties.startTime|流式处理定位符的开始时间。|
|properties.streamingLocatorId|流式处理定位符的 StreamingLocatorId。|
|properties.streamingPolicyName |此流式处理定位符使用的流式处理策略的名称。 指定创建的流式处理策略的名称，或使用一个预定义的流式处理策略。 可用的预定义流式处理策略包括：“Predefined_DownloadOnly”、“Predefined_ClearStreamingOnly”、“Predefined_DownloadAndClearStreaming”、“Predefined_ClearKey”、“Predefined_MultiDrmCencStreaming”和“Predefined_MultiDrmStreaming”|
|type|资源的类型。|

若要查看完整定义，请参阅[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)。

## <a name="filtering-ordering-paging"></a>筛选、排序、分页

媒体服务支持对流式处理定位符使用以下 OData 查询选项： 

* $filter 
* $orderby 
* $top 
* $skiptoken 

运算符说明：

* Eq = 等于
* Ne = 不等于
* Ge = 大于或等于
* Le = 小于或等于
* Gt = 大于
* Lt = 小于

### <a name="filteringordering"></a>筛选/排序

下表显示这些选项如何应用于 StreamingLocator 属性： 

|名称|筛选器|顺序|
|---|---|---|
|id |||
|名称|Eq、ne、ge、le、gt、lt|升序和降序|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |Eq、ne、ge、le、gt、lt|升序和降序|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |Eq、ne、ge、le、gt、lt|升序和降序|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

### <a name="pagination"></a>分页

已启用的四个排序顺序均支持分页。 当前，页面大小为 10。

> [!TIP]
> 应始终使用下一个链接来枚举集合，而不依赖特定的页面大小。

如果查询响应包含许多项，则服务将返回一个“\@odata.nextLink”属性来获取下一页结果。 这可用于逐页浏览整个结果集。 无法配置页面大小。 

如果在逐页浏览集合时创建或删除了 StreamingLocator，则会在返回的结果中反映此更改（如果这些更改位于集合中尚未下载的部分）。 

以下 C# 示例显示如何枚举帐户中的所有 StreamingLocator。

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

若要查看 REST 示例，请参阅[流式处理定位符 - 列表](https://docs.microsoft.com/rest/api/media/streaminglocators/list)

## <a name="next-steps"></a>后续步骤

[流式传输文件](stream-files-dotnet-quickstart.md)
