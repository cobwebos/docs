---
title: 什么是必应图像搜索 API？
titleSuffix: Azure Cognitive Services
description: 必应图像搜索 API 允许你在应用程序中使用必应的认知图像搜索功能。 使用此 API 发送用户搜索查询时，可以获取并显示与必应图像类似的相关高质量图像。
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 71e8e363a02e08a099d9051c7f851e11bbd9b80b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467275"
---
# <a name="what-is-the-bing-image-search-api"></a>什么是必应图像搜索 API？

必应图像搜索 API 允许你在应用程序中使用必应的认知图像搜索功能。 使用此 API 发送用户搜索查询时，可以获取并显示与[必应图像](https://www.bing.com/images)类似的相关高质量图像。

请注意，必应图像搜索 API 提供仅限图像的搜索结果。 对于其他类型的 Web 内容，请使用[必应 Web 搜索 API](../bing-web-search/search-the-web.md)、[视频搜索 API](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search) 和[新闻搜索 API](https://review.docs.microsoft.com/azure/cognitive-services/bing-news-search)。

## <a name="bing-image-search-features"></a>必应图像搜索功能

虽然必应图像搜索主要通过搜索查询来查找并返回相关图像，但此服务也提供多项其他功能，可以在 Web 上进行智能且集中化的图像检索。


| 功能                                                                                                                                                                                 | 说明                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [实时建议搜索词](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | 在用户键入时通过[必应自动建议 API](../bing-autosuggest/get-suggested-search-terms.md) 显示建议的搜索词改善应用体验。 |
| [筛选和限制图像结果](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#filtering-images)                       | 通过编辑查询参数筛选必应返回的图像。                                                                                                       |
| [缩略图的裁剪、重设大小和显示](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | 编辑和显示必应图像搜索返回的图像的缩略图预览。                                                                                      |
| [用户搜索查询的分段和扩展](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#pivoting-the-query)               | 通过包括和显示必应建议的查询搜索词，扩展搜索功能。                                                                    |
| [获取热门图像](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | 自定义一个搜罗全世界热门图像的搜索。                                                                                                          |

## <a name="workflow"></a>工作流

必应图像搜索 API 是一项 RESTful Web 服务，可以轻松地通过任何编程语言调用，只要该语言能够发出 HTTP 请求和分析 JSON 即可。 可以通过 [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) 或 [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) 使用此服务。

1. 创建可以访问必应搜索 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有 Azure 订阅，可以免费[创建一个帐户](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。
2. 使用有效的[搜索查询](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)向 API 发送请求。
3. 通过分析返回的 JSON 消息处理 API 响应。

## <a name="next-steps"></a>后续步骤

首先，请尝试必应搜索 API [交互式演示](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)。
此演示介绍如何快速自定义搜索查询并在 Web 中搜索图像。

做好调用 API 的准备后，请创建一个[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有 Azure 订阅，可以免费[创建一个帐户](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。

若要完成第一个 API 请求的快速入门，可以学习：

* 使用 REST API [向必应发送搜索查询](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp)，或者
* 使用 SDK [请求和筛选](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart)必应返回的图像。

## <a name="see-also"></a>另请参阅

* [必应图像搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) 参考部分包含有关终结点、标头、API 响应和查询参数的定义和信息，这些都可以用来请求基于图像的搜索结果。

* [必应使用和显示要求](./useanddisplayrequirements.md)指定了允许用户如何使用通过必应搜索 API 获得的内容和信息。

* [使用必应图像搜索 API 从 Web 获取图像](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)主题介绍了如何在 Web 中搜索和获取图像。

* [发送和使用搜索查询](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)主题介绍了如何完成搜索查询的发出、自定义和分段。
