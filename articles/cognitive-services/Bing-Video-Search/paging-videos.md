---
title: 如何翻页浏览可用视频 | Microsoft Docs
description: 演示如何翻页浏览必应可以返回的所有视频。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 910A485F-BCF3-42B9-958D-DD48BDEDA965
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 00476825eb3fc1008c3f2172b591d8b7a2f35884
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365411"
---
# <a name="paging-videos"></a>分页视频

调用视频搜索 API 时，必应会返回结果的列表。 此列表是与查询相关的所有结果的一部分。 若要获取估计的可用结果总数，请访问答案对象的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) 字段。  
  
以下示例显示了视频答案包含的 `totalEstimatedMatches` 字段。  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
若要翻页浏览可用视频，请使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) 和 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) 查询参数。  
  
`count` 参数指定响应中要返回的结果数。 可以在响应中请求的最大结果数为 105。 默认值为 35。 交付的实际数量可能小于请求的数量。

`offset` 参数指定要跳过的结果数。 `offset` 从零开始且应小于 (`totalEstimatedMatches` - `count`)。  
  
如果希望每页显示 20 个视频，可以将 `count` 设置为 20，并将 `offset` 设置为 0，以获得第一页结果。 对于每个后续页面，将按 20 增加 `offset`（例如，20、40）。  

以下显示了从偏移量 40 开始请求 20 个视频的示例。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

如果默认 `count` 值适用于你的实现，则只需指定 `offset` 查询参数。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

通常，如果一次翻页 35 个视频，则可以在第一次请求时将 `offset` 查询参数设置为 0，然后在每个后续请求中按 35 增加 `offset`。 但是，后续响应中的某些结果可能会与先前的响应重复。 例如，响应中的前两个视频可能与前一个响应中的最后两个视频相同。

若要消除重复结果，请使用 `Videos` 对象的 [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) 字段。

例如，如果希望一次翻页 30 个视频，则在第一个请求中将 `count` 设置为 30，并将 `offset` 设置为 0。 在下一个请求中，将 `offset` 查询参数设置为 `nextOffset` 值。


> [!NOTE]
> 分页仅适用于视频搜索 (/videos/search)，而不适用于视频见解 (/videos/details) 或热门视频 (/videos/trending)。