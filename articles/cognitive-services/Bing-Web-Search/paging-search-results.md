---
title: 如何浏览搜索结果 - 必应搜索 API
titleSuffix: Azure Cognitive Services
description: 了解如何从必应搜索 API 中浏览搜索结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481735"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>如何翻阅必应搜索 API 的结果

当您向必应 Web、自定义、图像、新闻或视频搜索 API 发送呼叫时，必应返回可能与查询相关的结果总数的子集。 要获取估计的可用结果总数，请访问答案对象的`totalEstimatedMatches`字段。 

例如： 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>通过搜索结果进行分页

要翻阅可用结果，请在发送请求时`count`使用`offset`和 查询参数。  

> [!NOTE]
>
> * 使用必应视频、图像和新闻 API 进行寻呼仅适用于常规视频 （）、`/video/search`新闻`/news/search`（ ）`/image/search`和图像 （ ） 搜索。 不支持通过趋势主题和类别进行分页。  
> * 该`TotalEstimatedMatches`字段是当前查询的搜索结果总数的估计。 设置 和`count``offset`参数时，此估计值可能会更改。

| 参数 | 描述                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | 指定要在响应中返回的结果数。 请注意，`count`的默认值和可能请求的最大结果数因 API 而异。 您可以在["后续步骤](#next-steps)"下的参考文档中找到这些值。 |
| `offset`  | 指定要跳过的结果数。 `offset` 从零开始，应小于 (`totalEstimatedMatches` - `count`)。                                           |

例如，如果要每页显示 15 个结果，则`count`设置为 15 和`offset`0 以获取结果的第一页。 对于每个后续 API 调用，您将`offset`增加 15。 下面的示例展示了如何从偏移 45 处开始请求获取 15 个网页。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

如果使用默认值`count`，则只需在 API 调用中指定`offset`查询参数。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

使用必应图像和视频 API 时，可以使用 该`nextOffset`值来避免重复搜索结果。 从`Images`或`Videos`响应对象获取该值，并在请求中使用 参数。 `offset`  

> [!NOTE]
> 必应 Web 搜索 API 返回搜索结果，这些结果可以包括网页、图像、视频和新闻。 当您翻阅 Bing Web 搜索 API 的搜索结果时，您只分页[网页](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)，而不是其他答案类型，如图像或新闻。 对象中的`WebPage`搜索结果可能还包括出现在其他答案类型中的结果。
>
> 如果使用`responseFilter`查询参数而不指定任何筛选器值，请不要使用 和`count``offset`参数。 

## <a name="next-steps"></a>后续步骤

* [什么是必应 Web 搜索 API？](bing-api-comparison.md)
* [必应 Web 搜索 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [必应自定义搜索 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [必应新闻搜索 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [必应视频搜索 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [必应图像搜索 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
