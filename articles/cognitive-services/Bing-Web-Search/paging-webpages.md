---
title: 如何逐页浏览可访问网页 | Microsoft Docs
description: 介绍了如何逐页浏览必应可以返回的所有网页。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: bf29783246c603270d59b20b63027fccdbd45b89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365443"
---
# <a name="paging-webpages"></a>分页网页 

如果调用 Web 搜索 API，必应会返回结果列表。 此列表是与查询相关的所有结果的一部分。 若要获取可访问结果的总估计数，请访问答案对象的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#totalestimatedmatches) 字段。  
  
下面的示例展示了 Web 答案对象包含的 `totalEstimatedMatches` 字段。  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
若要逐页浏览可访问网页，请使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) 和 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset) 查询参数。  
  
`count` 参数指定要在响应中返回的结果数。 最多可以请求在响应中获取 50 个结果。 默认值为 10。 提供的实际结果数可能小于请求获取的结果数。

`offset` 参数指定要跳过的结果数。 `offset` 从零开始，应小于 (`totalEstimatedMatches` - `count`)。  
  
若要每页显示 15 个网页，可以将 `count` 设置为 15，并将 `offset` 设置为 0，以获取第一页结果。 对于每个后续网页请求，让 `offset` 递增 15（例如，15、30）。  
  
下面的示例展示了如何从偏移 45 处开始请求获取 15 个网页。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

如果默认 `count` 值适用于实现，只需指定 `offset` 查询参数即可。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Web 搜索 API 返回的结果包括网页，也可能包括图像、视频和新闻。 对搜索结果进行分页是分页 [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) 答案，而不是分页图像或新闻等其他答案。 例如，如果将 `count` 设置为 50，就会返回 50 个网页结果，但响应中可能还有其他答案的结果。 例如，响应可能包括 15 张图像和 4 篇新闻文章。 结果也可能在第一页（而不是第二页）上显示新闻，反之亦然。   
    
如果指定 `responseFilter` 查询参数，但未在筛选器列表中添加“网页”，请勿使用 `count` 和 `offset` 参数。  
