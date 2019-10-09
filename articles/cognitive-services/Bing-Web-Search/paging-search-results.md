---
title: 如何逐页浏览搜索结果-必应搜索 API
titleSuffix: Azure Cognitive Services
description: 了解如何逐页浏览必应搜索 API 的搜索结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: aahi
ms.openlocfilehash: 9fc05ab42c75bac1f8e192dd4fe20bb142881479
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176910"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>如何逐页浏览必应搜索 API 的结果

当你将调用发送到必应 Web、自定义、图像、新闻或视频搜索 Api 时，必应会返回可能与查询相关的结果总数的子集。 若要获取估计的可用结果总数，请访问应答对象的 `totalEstimatedMatches` 字段。 

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

## <a name="paging-through-search-results"></a>在搜索结果中分页

若要逐页浏览可用结果，请在发送请求时使用 `count` 和 @no__t 查询参数。  

> [!NOTE]
>
> * 带有必应视频、图像和新闻 Api 的分页仅适用于常规视频（`/video/search`）、新闻（`/news/search`）和图像（`/image/search`）搜索。 不支持通过趋势主题和类别进行分页。  
> * @No__t 字段是对当前查询的搜索结果总数的估计值。 设置 @no__t 0 和 @no__t 参数时，此估计值可能会发生变化。

| 参数 | 描述                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | 指定要在响应中返回的结果数。 请注意，@no__t 的默认值为-0，并且可能请求的最大结果数因 API 而异。 可以在 "[后续步骤](#next-steps)" 下的参考文档中找到这些值。 |
| `offset`  | 指定要跳过的结果数。 `offset` 从零开始，应小于 (`totalEstimatedMatches` - `count`)。                                           |

例如，如果想要每页显示15个结果，请将 @no__t 0 设置为15，将 `offset` 设置为0，以获取结果的第一页。 对于后续的每个 API 调用，将 `offset` 递增15。 下面的示例展示了如何从偏移 45 处开始请求获取 15 个网页。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

如果使用默认的 `count` 值，则只需在 API 调用中指定 @no__t 查询参数即可。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> 必应 Web 搜索 API 返回可包含网页、图像、视频和新闻的搜索结果。 当你从必应 Web 搜索 API 中翻到搜索结果[时，你只会](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)分页，而不是其他答案类型，如图像或新闻。 @No__t-0 对象中的搜索结果也可能包含其他答案类型中显示的结果。
>
> 如果在未指定任何筛选器值的情况下使用 @no__t 0 查询参数，请不要使用 @no__t 和 `offset` 参数。 

## <a name="next-steps"></a>后续步骤

* [什么是必应 Web 搜索 Api？](bing-api-comparison.md)
* [必应 Web 搜索 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing 自定义搜索 API v7 引用](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [必应新闻搜索 API v7 引用](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [必应视频搜索 API v7 引用](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [必应图像搜索 API v7 引用](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)