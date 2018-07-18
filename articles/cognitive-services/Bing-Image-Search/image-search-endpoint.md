---
title: 图像搜索终结点 | Microsoft Docs
description: 图像搜索 API 终结点摘要。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 11/30/2017
ms.author: v-gedod
ms.openlocfilehash: 0d5f28bfdb45b27b04df068f75e8a20d0235d12b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365400"
---
# <a name="image-search-endpoints"></a>图像搜索终结点
图像搜索 API 包括 3 个终结点。  终结点 1 基于查询返回来自 Web 的图像。 终结点 2 返回 [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse)。  终结点 3 返回热门图像。
## <a name="endpoints"></a>终结点
若要使用必应 API 获取图像结果，请向以下某个终结点发送请求。 使用标头和 URL 参数来定义更多规范。

终结点 1：返回与 `?q=""` 定义的用户搜索查询相关的图像。
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

终结点 2：使用有关 `GET` 或 `POST` 返回关于图像的见解。
``` 
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
GET 请求返回有关图像的见解，例如包含图像的网页。 包括 [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) 参数与 `GET` 请求。

或者，你可以在 `POST` 请求的正文中包含二进制图像，并将 [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) 参数设置为 `RecognizedEntities`。 这将返回 [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken)，用作后续 `GET` 请求中的参数，该参数返回图像中的有关人的信息。  将 `modules` 设置为 `All` 获取所有见解（`POST` 的结果中的 `RecognizedEntities` 除外），无需使用 `insightsToken` 进行其他调用。 


终结点 3：基于其他人的搜索请求返回热门图像。 这些图像被分成不同类别，例如，基于值得注意的人或事件。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

有关支持热门图像的市场列表，请参阅[热门图像](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)。

有关标头、参数、市场代码、响应对象、错误等详细信息，请参阅[必应图像搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) 参考。
## <a name="response-json"></a>响应 JSON
对图像搜索请求的响应包括作为 JSON 对象的结果。 有关分析结果的示例，请参阅[教程](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)和[源代码](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source)。

## <a name="next-steps"></a>后续步骤
必应 API 支持根据其类型返回结果的搜索操作。 所有搜索终结点均将结果作为 JSON 响应对象返回。  所有终结点支持按经度、纬度和搜索半径返回特定语言和/或位置的查询。

有关每个终结点支持的参数的完整信息，请参阅每个类型的参考页面。
有关使用图像搜索 API 的基本请求的示例，请参阅[图像搜索快速入门](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web)。