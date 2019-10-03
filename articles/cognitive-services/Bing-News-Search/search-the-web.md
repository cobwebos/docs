---
title: 什么是必应新闻搜索 API？
titleSuffix: Azure Cognitive Services
description: 了解如何使用必应新闻搜索 API 在 Web 上搜索多个类别的当前标题，包括标题和热门主题。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 06/19/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 577ce53b4667928d7eb5a870f57ff7180caaf6f5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423660"
---
# <a name="what-is-the-bing-news-search-api"></a>什么是必应新闻搜索 API？

使用必应新闻搜索 API，可以轻松将必应的认知新闻搜索功能集成到应用程序中。 此 API 提供的体验与[必应新闻](https://www.bing.com/news)类似，可以让你发送搜索查询并接受相关的新闻文章。

请注意，必应新闻搜索 API 仅提供新闻搜索结果。 对于其他类型的 Web 内容，请使用[必应 Web 搜索 API](../bing-web-search/search-the-web.md)、[视频搜索 API](../bing-video-search/search-the-web.md) 和[图像搜索 API](../bing-image-search/overview.md)。

## <a name="bing-news-search-api-features"></a>必应新闻搜索 API 功能

虽然必应新闻搜索 API 主要用于查找并返回相关的新闻文章，但它还提供了一些在网络上进行智能化、集中化新闻检索的功能。

|Feature  |说明  |
|---------|---------|
|[建议和使用搜索词](concepts/search-for-news.md#suggest-and-use-search-terms)     | 在用户键入时通过[必应自动建议 API](../bing-autosuggest/get-suggested-search-terms.md) 显示建议的搜索词改善搜索体验。         |
|[获取一般新闻](concepts/search-for-news.md#get-general-news)     | 查找新闻时，可以向必应新闻搜索 API 发送搜索查询，然后就会获得相关新闻文章的列表。           |
|[当天的头条新闻](concepts/search-for-news.md#get-todays-top-news)      | 获取当天的头条新闻故事，涵盖所有类别。       |
|[按类别的新闻](concepts/search-for-news.md)     | 搜索具体类别的新闻。        | 
|[标题新闻](concepts/search-for-news.md)     | 跨所有类别搜索头条标题新闻。         |

## <a name="workflow"></a>工作流

必应新闻搜索 API 是一项 RESTful Web 服务，可以轻松地通过任何编程语言调用，只要该语言能够发出 HTTP 请求和分析 JSON 即可。 可以通过 REST API 或 SDK 使用此服务。

1. 创建可以访问必应搜索 API 的认知服务 API 帐户。 如果没有 Azure 订阅，可以[免费创建一个帐户](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api)。

2. 使用有效的搜索查询向 API 发送请求。

3. 通过分析返回的 JSON 消息处理 API 响应。

## <a name="next-steps"></a>后续步骤

首先，请尝试必应新闻搜索 API 的[交互式演示](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)。 此演示介绍如何快速自定义搜索查询并在 Web 中查找新闻。

若要完成第一个 API 请求的快速入门，请尝试 [REST API](quickstart.md) 或某个 [SDK](sdk.md) 的快速入门。

## <a name="see-also"></a>另请参阅

* [必应新闻搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) 参考部分包含有关终结点、标头、API 响应和查询参数的定义和信息，这些都可以用来请求基于图像的搜索结果。

* [必应使用和显示要求](./useanddisplayrequirements.md)指定了允许用户如何使用通过必应搜索 API 获得的内容和信息。
