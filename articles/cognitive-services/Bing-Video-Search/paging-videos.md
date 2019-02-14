---
title: 如何翻页浏览可用视频 - 必应视频搜索
titlesuffix: Azure Cognitive Services
description: 了解如何翻页浏览必应视频搜索 API 返回的视频。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: d75a71fe5ba5a275aa8703bc59398cc8a7a5a622
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867131"
---
# <a name="paging-through-video-search-results"></a>翻页浏览视频搜索结果

必应视频搜索 API 返回会它为查询找到的所有搜索结果的子集。 通过对该 API 进行后续调用来翻页浏览这些结果，可以获取它们并显示在应用程序中。

> [!NOTE]
> 分页仅适用于视频搜索 (/videos/search)，而不适用于视频见解 (/videos/details) 或热门视频 (/videos/trending)。

## <a name="total-estimated-matches"></a>估计匹配项总数

若要获取找到的搜索结果估计数量，请使用 JSON 响应中的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) 字段。   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>翻页浏览视频

若要翻页浏览可用视频，请在发送请求时使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) 和 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) 查询参数。  
  

|参数  |说明  |
|---------|---------|
|`count`     | 指定要在响应中返回的结果数。 可以在响应中请求的最大结果数为 100。 默认值为 10。 提供的实际结果数可能小于请求获取的结果数。        |
|`offset`     | 指定要跳过的结果数。 `offset` 从零开始，应小于 (`totalEstimatedMatches` - `count`)。          |

例如，如果希望每页显示 20 篇文章，可以将 `count` 设置为 20，并将 `offset` 设置为 0，以获得第一页结果。 对于每个后续页面，将按 20 增加 `offset`（例如，20、40）。  
  
下面的示例从偏移 40 处开始请求获取 20 个视频。  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

如果对 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) 使用默认值，则只需指定 `offset` 查询参数，如以下示例所示。  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

如果一次翻页浏览 35 个视频，则可以在第一次请求时将 `offset` 查询参数设置为 0，然后在每个后续请求中按 35 增加 `offset`。 但是，下一个响应中的某些结果可能包含前一个响应中的重复视频结果。 例如，响应中的前两个视频可能与前一个响应中的最后两个视频相同。

若要消除重复结果，请使用 `Videos` 对象的 [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) 字段。

例如，如果希望一次翻页浏览 30 个视频，则可以在第一个请求中将 `count` 设置为 30，并将 `offset` 设置为 0。 在下一个请求中，将 `offset` 查询参数设置为 `nextOffset` 值。

> [!NOTE]
> `TotalEstimatedAnswers` 字段是可以为当前查询检索的搜索结果总数的估计值。  设置 `count` 和 `offset` 参数时，`TotalEstimatedAnswers` 数值可能会更改。 
  
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
[获取视频见解](video-insights.md)
