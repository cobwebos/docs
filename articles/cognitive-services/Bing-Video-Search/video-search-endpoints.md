---
title: 视频搜索终结点 - 必应视频搜索
titlesuffix: Azure Cognitive Services
description: 必应视频搜索 API 终结点的摘要。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: rosh
ms.openlocfilehash: c153f577f76944d22f9a1b0fb4b24d332d2a02c8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220765"
---
# <a name="video-search-endpoints"></a>视频搜索终结点
视频搜索 API 有 3 个终结点。  终结点 1 根据查询从 Web 返回视频。 终结点 2 根据 `modules` URL 参数返回与视频有关的见解。  终结点 3 返回热门图像。

## <a name="endpoints"></a>终结点
若要使用必应 API 获取视频结果，请向以下终结点之一发送 `GET` 请求。 使用请求头和 URL 参数可以定义更多规范。

**终结点 1**：返回与 `?q=""` 定义的用户搜索查询相关的视频。
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**终结点 2**：返回与视频有关的见解（如相关视频）。 添加 `modules` [查询参数](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters)，这是响应请求的见解的逗号分隔列表。
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**终结点 3**：返回根据其他人的搜索请求评出的热门视频。 这些视频分成不同类别（例如，值得关注的人或事件）。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

若要详细了解请求头、参数、市场代码、响应对象、错误等，请参阅[必应视频搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) 参考。
## <a name="response-json"></a>响应 JSON
视频搜索请求响应包含 JSON 对象形式的结果。 有关分析结果的示例，请参阅[教程](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app)和[源代码](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source)。

## <a name="next-steps"></a>后续步骤
必应 API 支持根据其类型返回结果的搜索操作。 所有搜索终结点均将结果作为 JSON 响应对象返回。  所有终结点支持后列查询：按经度、纬度和搜索半径返回特定语言和/或位置的查询。

若要完整了解每个终结点支持的参数，请参阅每种类型对应的参考页面。
有关使用视频搜索 API 的基本请求示例，请参阅[视频搜索快速入门](https://docs.microsoft.com/azure/cognitive-services/bing-video-search)。