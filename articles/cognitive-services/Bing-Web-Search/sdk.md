---
title: 必应搜索 SDK | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 适用于在网上进行搜索的应用程序的必应 Web 搜索 SDK。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 1/11/2018
ms.author: v-gedod
ms.openlocfilehash: 9d2f363d772febfb9fffd2d9c2924398e438d6e0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366696"
---
# <a name="bing-search-sdk"></a>必应搜索 SDK
必应 Web 搜索 API 示例包括执行以下操作的方案：
1. 使用单字查询来获取结果，并输出第一个网页、图像、新闻和视频结果的名称和 URL。
2. 查询段落，验证结果数，并输出第一个结果的名称和 URL。
3. 在响应筛选器设置为 `news` 的情况下查询搜索词，并输出新闻结果的详细信息。
4. 使用 `answerCount` 和 `promote` 参数查询搜索词，然后输出结果的详细信息。

必应搜索 SDK 使 Web 搜索功能可采用以下编程语言进行访问：
* 开始使用 [.NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
    * 有关定义和依赖项，另请参阅 [.NET 库](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingWebSearch)。
* 开始使用 [Node.js 示例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * 有关定义和依赖项，另请参阅 [Node.js 库](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/webSearch)。
* 开始使用 [Java 示例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * 有关定义和依赖项，另请参阅 [Java 库](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)。
* 开始使用 [Python 示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * 有关定义和依赖项，另请参阅 [Python 库](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-websearch)。

每种语言的 SDK 示例包括一个自述文件，其中包含有关先决条件和安装/运行示例的详细信息。
