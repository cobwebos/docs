---
title: 快速入门：执行新闻搜索 - 适用于 Node.js 的必应新闻搜索 SDK
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，可以使用适用于 Node.js 的必应新闻搜索 SDK 搜索新闻，并处理响应。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 0fa78474d6400f890626859c32915c8657f92d4e
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249339"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>快速入门：使用适用于 Node.js 的必应新闻搜索 SDK 执行新闻搜索

必应新闻搜索 SDK 包含用于新闻查询以及对结果进行分析的 REST API 功能。 

Git Hub 上提供了 [Node 必应新闻搜索 SDK 示例的源代码](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js)。

## <a name="application-dependencies"></a>应用程序依赖项
在“搜索”下获取[认知服务访问密钥](https://azure.microsoft.com/try/cognitive-services/)。  另请参阅[认知服务定价 - 必应搜索 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。 

若要使用必应新闻搜索 SDK 来设置控制台应用程序，请执行以下操作：
* 在开发环境中运行 `npm install ms-rest-azure`。
* 在开发环境中运行 `npm install azure-cognitiveservices-newssearch`。

## <a name="news-search-client"></a>新闻搜索客户端
在“搜索”下获取[认知服务访问密钥](https://azure.microsoft.com/try/cognitive-services/)。 创建 `CognitiveServicesCredentials` 的实例：
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
然后对该客户端进行实例化：
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
使用客户端通过查询文本进行搜索，在本例中查询文本为“Winter Olympics”：
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
代码会将 `result.value` 项输出至控制台，并且不会分析任何文本。 结果（如果每个类别都有结果）将包括：
- _type:'NewsArticle'
- _type:'WebPage'
- _type:'VideoObject'
- _type:'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>后续步骤

[认知服务 Node.js SDK 示例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
