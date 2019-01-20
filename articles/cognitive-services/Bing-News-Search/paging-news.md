---
title: 如何分页列出必应新闻搜索结果
titlesuffix: Azure Cognitive Services
description: 了解如何分页列出必应新闻搜索 API 返回的新闻文章。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 9acf0c26707dab9de443b06e7314de6d77913777
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258248"
---
# <a name="how-to-page-through-news-search-results"></a>如何分页列出新闻搜索结果

调用新闻搜索 API 时，必应会返回与查询相关的结果列表。 若要获取可访问结果的总估计数，请访问答案对象的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) 字段。  
  
以下示例显示了新闻答案包含的 `totalEstimatedMatches` 字段。  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
若要翻页浏览可用文章，请使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) 和 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) 查询参数。  
 

|参数  |Description  |
|---------|---------|
|`count`     | 指定要在响应中返回的结果数。 可以在响应中请求的最大结果数为 100。 默认值为 10。 提供的实际结果数可能小于请求获取的结果数。        |
|`offset`     | 指定要跳过的结果数。 `offset` 从零开始，应小于 (`totalEstimatedMatches` - `count`)。          |

例如，如果希望每页显示 20 篇文章，可以将 `count` 设置为 20，并将 `offset` 设置为 0，以获得第一页结果。 对于每个后续页面，将按 20 增加 `offset`（例如，20、40）。  
  
下面的示例展示了如何从偏移 40 处开始请求 20 篇新闻文章。  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
如果 `count` 默认值适用于实现，请仅指定 `offset` 查询参数，如以下示例所示：  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> 翻页仅适用于新闻搜索 (/news/search)，而不适用于热门主题 (/news/trendingtopics) 或新闻类别 (/news)。

> [!NOTE]
> `TotalEstimatedAnswers` 字段是可以为当前查询检索的搜索结果总数的估计值。  设置 `count` 和 `offset` 参数时，`TotalEstimatedAnswers` 数值可能会更改。 