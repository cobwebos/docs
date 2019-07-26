---
title: 什么是必应视频搜索 API？
titleSuffix: Azure Cognitive Services
description: 了解如何使用必应视频搜索 API 在 Web 中搜索视频。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 06/26/2019
ms.author: scottwhi
ms.openlocfilehash: 4f3a52590a9a0c50bdc87f87792027333fc5269f
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500152"
---
# <a name="what-is-the-bing-video-search-api"></a>什么是必应视频搜索 API？

使用必应视频搜索 API，可以轻松地将视频搜索功能添加到服务和应用程序中。 使用此 API 发送用户搜索查询时，可以获取并显示与[必应视频](https://www.bing.com/video)类似的相关高质量视频。 将此 API 用于只包含视频的搜索结果。 [必应 Web 搜索 API](../bing-web-search/search-the-web.md) 可以返回其他类型的 Web 内容，包括网页、视频、新闻和图像。

## <a name="bing-video-search-api-features"></a>必应视频搜索 API 功能

| Feature                                                                                                                                                                                 | 说明                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [实时建议搜索词](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | 在用户键入时通过[必应自动建议 API](../bing-autosuggest/get-suggested-search-terms.md) 显示建议的搜索词改善应用体验。 |
| [筛选和限制视频结果](concepts/get-videos.md#filtering-videos)                      | 通过编辑查询参数筛选返回的视频。                                                                                                       |
| [缩略图的裁剪、重设大小和显示](../bing-web-search/resize-and-crop-thumbnails.md)                                                | 编辑和显示必应视频搜索 API 返回的视频的缩略图预览。                                                                                      |
| [获取热门视频](trending-videos.md) | 从全世界搜索热门视频。                                                                                                          |
| [获取视频见解](video-insights.md) | 自定义一个从全世界搜罗热门视频的搜索。                                                                                                          |

## <a name="workflow"></a>工作流

必应视频搜索 API 是一项 RESTful Web 服务，可以轻松地通过任何编程语言进行调用，只要该语言能够发出 HTTP 请求和分析 JSON 即可。 可以通过 [REST API](csharp.md) 或 [SDK](video-search-sdk-quickstart.md) 使用此服务。

1. 创建可以访问必应搜索 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有 Azure 订阅，可以免费[创建一个帐户](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。
2. 使用有效的搜索查询向 API 发送请求。
3. 通过分析返回的 JSON 消息处理 API 响应。


## <a name="next-steps"></a>后续步骤

必应视频搜索 API [交互演示](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)介绍如何自定义搜索查询并在 Web 中搜索视频。

做好调用 API 的准备后，请创建一个[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有 Azure 订阅，可以免费[创建一个帐户](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。

通过[快速入门](csharp.md)完成第一个 API 请求的快速入门。

## <a name="see-also"></a>另请参阅

* [必应视频搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) 参考页包含用来请求搜索结果的终结点、标头和查询参数的列表。

* [必应使用和显示要求](./useanddisplayrequirements.md)指定了允许用户如何使用通过必应搜索 API 获得的内容和信息。