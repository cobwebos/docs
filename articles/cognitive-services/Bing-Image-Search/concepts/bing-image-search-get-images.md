---
title: 从 Web 获取图像 - 必应图像搜索 API
titleSuffix: Azure Cognitive Services
description: 使用必应图像搜索 API 在 Web 中搜索并获取相关图像。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: f54003f4e1c60b80b500f49bb83d4b7adf2bc12a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312859"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>使用必应图像搜索 API 从 Web 获取图像

使用必应图像搜索 REST API 时，可以通过发送以下 GET 请求从 Web 获取与你的搜索词相关的图像：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!IMPORTANT]
> * 所有请求必须从服务器发出，不得从客户端发出。
> * 如果是首次调用任何必应搜索 API，请勿包括客户端 ID 标头。 只有在以前调用过必应 API 且该 API 针对用户和设备组合返回了客户端 ID 的情况下，才包括客户端 ID。
> * 必须按响应中提供的顺序显示图像。

## <a name="get-images-from-a-specific-web-domain"></a>从特定 Web 域获取图像

若要从特定的域获取图像，请使用 [site:](https://msdn.microsoft.com/library/ff795613.aspx) 查询运算符。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> 当查询使用 `site:` 运算符时，不管 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) 设置如何，对查询的响应都可能包含成人内容。 只有在知道域内容的情况下，才使用 `site:`。

以下示例演示了如何从 ContosoSailing.com 获取必应在过去一周发现的小图像。  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filter-images"></a>筛选图像

 默认情况下，图像搜索 API 返回与查询相关的所有图像。 若要筛选必应返回的图像（例如，只返回背景透明的图像或特定大小的图像），请使用以下查询参数：

* [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect)：按纵横比筛选图像（例如，标准图像或宽屏图像）。
* [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color)：按主色或黑白色筛选图像。
* [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness)：按时间筛选图像（例如，必应在过去一周发现的图像）。
* [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height)、[width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width)：按高度和宽度筛选图像。
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent)：按内容筛选图像（例如，只显示人脸的图像）。
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype)：按类型筛选图像（例如，剪贴画、动态 GIF 或透明背景图像）。
* [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license)：按与站点关联的许可证类型筛选图像。
* [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size)：按大小筛选图像（例如，最大尺寸为 200x200 像素的小图像）。

若要从特定的域获取图像，请使用 [site:](https://msdn.microsoft.com/library/ff795613.aspx) 查询运算符。

 > [!NOTE]
 > 当查询使用 `site:` 运算符时，不管 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) 设置如何，对查询的响应都可能包含成人内容。 只有在知道域内容的情况下，才使用 `site:`。

以下示例演示了如何从 ContosoSailing.com 获取必应在过去一周发现的小图像。  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>必应图像搜索响应格式

必应提供的响应消息包含一个 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 应答，该应答包含认知服务确定与查询相关的图像的列表。 列表中的每个 [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) 对象包含图像的以下信息：URL、大小、尺寸、编码格式、缩略图的 URL，以及缩略图的尺寸。

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

调用必应图像搜索 API 时，必应会返回结果的列表。 此列表是与查询相关的所有结果的一部分。 响应的 `totalEstimatedMatches` 字段包含一个估计数，是对可以查看的图像数的估计。 有关如何逐页查看剩余图像的详细信息，请参阅[对图像进行分页](../paging-images.md)。

## <a name="next-steps"></a>后续步骤

如果从未尝试过必应图像搜索 API，请尝试[快速入门](../quickstarts/csharp.md)。 如果需要更复杂的内容，请尝试关于如何创建[单页 Web 应用](../tutorial-bing-image-search-single-page-app.md)的教程。
