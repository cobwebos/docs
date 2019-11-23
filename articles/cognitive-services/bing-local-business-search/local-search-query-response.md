---
title: Sending and using API queries and responses - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: 在本文中了解如何通过必应当地企业搜索 API 发送和使用搜索查询。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 25bcdb89002fec4f9b67b091996d7bf80bcf21c8
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326719"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>发送和使用必应当地企业搜索 API 查询和响应

可以通过向必应当地企业搜索 API 的终结点发送搜索查询并包含必需的 `Ocp-Apim-Subscription-Key` 标头，从该 API 获取当地查询结果。 除了使用可用的[标头](local-search-reference.md#headers)和[参数](local-search-reference.md#query-parameters)以外，还可以通过指定要搜索的区域的[地理边界](specify-geographic-search.md)以及要返回的地点的[类别](local-search-query-response.md)，来自定义搜索。

## <a name="creating-a-request"></a>创建请求

若要将请求发送到必应当地企业搜索 API，请将一个搜索词追加到 `q=` 参数，然后将该参数添加到 API 终结点并包含 `Ocp-Apim-Subscription-Key` 标头。 例如：

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

完整的请求 URL 语法如下所示。 有关发送请求的详细信息，请参阅必应当地企业搜索 API [快速入门](quickstarts/local-quickstart.md)，以及[标头](local-search-reference.md#headers)和[参数](local-search-reference.md#query-parameters)的参考内容。 

有关当地搜索类别的信息，请参阅[必应当地企业搜索 API 的搜索类别](local-categories.md)。

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>使用响应

必应当地企业搜索 API 的 JSON 响应包含 `SearchResponse` 对象。 API 将在 `places` 字段中返回相关搜索结果。 如果未找到任何结果，`places` 字段不会包含在响应中。

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>搜索结果属性

API 返回的 JSON 结果包含以下属性：

* _type
* 地址
* entityPresentationInfo
* 地区
* id
* name
* routeablePoint
* telephone
* url

有关标头、参数、市场代码、响应对象、错误等的一般信息，请参阅[必应当地搜索 API v7](local-search-reference.md) 参考。

> [!NOTE]
> 你或代表你的第三方不得出于测试、开发、培训、分发或提供任何非 Microsoft 服务或功能目的使用、保留、存储、缓存、共享或分发来自当地搜索 API 的任何数据。 


## <a name="example-json-response"></a>示例 JSON 响应

以下 JSON 响应包含查询 `?q=restaurant+in+Bellevue` 指定的搜索结果。

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>后续步骤
- [当地企业搜索快速入门](quickstarts/local-quickstart.md)
- [当地企业搜索 Java 快速入门](quickstarts/local-search-java-quickstart.md)
- [当地企业搜索 Node 快速入门](quickstarts/local-search-node-quickstart.md)
- [当地企业搜索 Python 快速入门](quickstarts/local-search-python-quickstart.md)
