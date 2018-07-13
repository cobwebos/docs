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
ms.openlocfilehash: f2f545a5a9195fc65515ea716f277723600cbb78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365404"
---
# <a name="paging-webpages"></a>分页网页 

调用自定义搜索 API 时，必应会返回结果列表。 此列表是与查询相关的所有结果的一部分。 若要获取估计的可用结果总数，请访问答案对象的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) 字段。  
  
以下示例显示了 Web 答案包含的 `totalEstimatedMatches` 字段。  
  
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
  
`count` 参数指定响应中要返回的结果数。 可以在响应中请求的最大结果数为 50。 默认值为 10。 交付的实际数量可能小于请求的数量。

`offset` 参数指定要跳过的结果数。 `offset` 从零开始且应小于 (`totalEstimatedMatches` - `count`)。  
  
如果希望每页显示 15 个网页，可以将 `count` 设置为 15，并将 `offset` 设置为 0，以获得第一页结果。 对于每个后续页面，将按 15 增加 `offset`（例如，15、30）。  
  
以下显示了从偏移量 45 开始请求 15 个网页的示例。  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

如果默认 `count` 值适用于你的实现，则只需指定 `offset` 查询参数。  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

