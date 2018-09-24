---
title: 必应自定义搜索：翻页浏览可用的网页 | Microsoft Docs
description: 演示如何翻页浏览必应可以返回的所有网页。
services: cognitive-services
author: brapel
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 5692776736090c55547c2fe934e2c0aaf840d3c5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982352"
---
# <a name="paging-webpages"></a>分页网页 

调用自定义搜索 API 时，必应会返回结果列表。 此列表是与查询相关的所有结果的一部分。 若要获取可访问结果的总估计数，请访问答案对象的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) 字段。  
  
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
  
若要翻页浏览可用网页，请使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#count) 和 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#offset) 查询参数。  
  
`count` 参数指定要在响应中返回的结果数。 最多可以请求在响应中获取 50 个结果。 默认值为 10。 提供的实际结果数可能小于请求获取的结果数。

`offset` 参数指定要跳过的结果数。 `offset` 从零开始，应小于 (`totalEstimatedMatches` - `count`)。  
  
若要每页显示 15 个网页，可以将 `count` 设置为 15，并将 `offset` 设置为 0，以获取第一页结果。 对于每个后续页面，将按 15 增加 `offset`（例如，15、30）。  
  
以下显示了从偏移量 45 开始请求 15 个网页的示例。  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

如果默认 `count` 值适用于你的实现，则只需指定 `offset` 查询参数。  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

