---
title: 必应搜索 SDK | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 适用于在网上进行搜索的应用程序的必应搜索 SDK。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: 4a40ea665e153536d2322706b455598902ce41eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366690"
---
# <a name="bing-search-sdk"></a>必应搜索 SDK
必应新闻搜索 API 示例包括执行以下操作的方案：
1. 通过 `market` 和 `count` 参数在新闻中查询搜索词，验证结果数，并输出第一个新闻结果的 `totalEstimatedMatches`、名称、URL、说明、发布时间以及提供者的名称。
2. 通过 `freshness` 和 `sortBy` 参数在最新新闻中查询搜索词，验证结果数，并输出第一个新闻结果的 `totalEstimatedMatches`、URL、说明、发布时间以及提供者的名称。
3. 使用安全搜索查询有关 `movie` 和 `TV entertainment` 的分类新闻，验证结果数，并输出第一个新闻结果的类别、名称、URL、说明、发布时间以及提供者的名称。
4. 在必应中查询新闻热门主题，验证结果数，并输出第一个新闻结果的名称、查询文本、`webSearchUrl`、`newsSearchUrl` 和图像 URL。

必应搜索 SDK 使 Web 搜索功能可采用以下编程语言进行访问：
* 开始使用 [.NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * 有关定义和依赖项，另请参阅 [.NET 库](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch)。
* 开始使用 [Node.js 示例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * 有关定义和依赖项，另请参阅 [Node.js 库](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch)。
* 开始使用 [Java 示例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * 有关定义和依赖项，另请参阅 [Java 库](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch)。
* 开始使用 [Python 示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * 有关定义和依赖项，另请参阅 [Python 库](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch)。

每种语言的 SDK 示例包括一个自述文件，其中包含有关先决条件和安装/运行示例的详细信息。