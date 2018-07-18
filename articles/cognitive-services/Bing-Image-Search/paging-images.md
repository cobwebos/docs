---
title: 如何逐页浏览可访问图像 | Microsoft Docs
description: 介绍了如何逐页浏览必应可以返回的所有图像。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a74ee817e84be5bb563c5fdaf25afc1dc14732e5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365396"
---
# <a name="paging-results"></a>分页结果

如果调用图像搜索 API，必应会返回结果列表。 此列表是与查询相关的所有结果的一部分。 若要获取可访问结果的总估计数，请访问答案对象的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) 字段。  
  
下面的示例展示了图像答案对象包含的 `totalEstimatedMatches` 字段。  
  
```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  
  
若要逐页浏览可访问图像，请使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) 和 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) 查询参数。  
  
`count` 参数指定要在响应中返回的结果数。 最多可以请求在响应中获取 150 个结果。 默认值为 35。 提供的实际结果数可能小于请求获取的结果数。

`offset` 参数指定要跳过的结果数。 `offset` 从零开始，应小于 (`totalEstimatedMatches` - `count`)。  
  
若要每页显示 20 个图像，可以将 `count` 设置为 20，并将 `offset` 设置为 0，以获取第一页结果。 下面的示例展示了如何从偏移 40 处开始请求获取 20 个图像。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

如果默认 `count` 值适用于实现，只需指定 `offset` 查询参数即可。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
若要一次逐页浏览 35 个图像，建议在第一次请求时将 `offset` 查询参数设置为 0，然后在每个后续请求中让 `offset` 递增 35。 不过，后续响应中的部分结果可能会与先前的响应结果重复。 例如，响应中的前两张图像可能与上一个响应中的最后两张图像相同。

若要消除重复结果，请使用 `Images` 对象的 [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) 字段。 `nextOffset` 字段指明对下一个请求使用的 `offset`。 例如，若要一次逐页浏览 30 个图像，请在第一个请求中将 `count` 设置为 30，并将 `offset` 设置为 0。 在下一个请求中，将 `count` 设置为 30，并将 `offset` 设置为上一个响应的 `nextOffset` 值。 若要向后逐页浏览，建议保留过往偏移的堆栈，并迅速设置最新偏移。

> [!NOTE]
> 分页仅适用于搜索图像（/图像/搜索），不适用于获取图像见解或热门图像（/图像/热门）。