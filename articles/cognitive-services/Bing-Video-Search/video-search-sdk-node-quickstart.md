---
title: 快速入门：必应视频搜索 SDK、Node
titleSuffix: Azure Cognitive Services
description: 设置必应视频搜索 SDK 控制台应用程序。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: rosh
ms.openlocfilehash: 1962ce07e4031708ea8c9ee308a016f4b9a1b018
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194817"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>快速入门：必应视频搜索 SDK 与 Node

必应视频搜索 SDK 包含 REST API 的功能，可用于视频查询和分析结果。 

Git Hub 上提供 [Node 必应视频搜索 SDK 示例的源代码](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js)。

## <a name="application-dependencies"></a>应用程序依赖项
在“搜索”下获取[认知服务访问密钥](https://azure.microsoft.com/try/cognitive-services/)。  另请参阅[认知服务定价 - 必应搜索 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

若要使用必应视频搜索 SDK 来设置控制台应用程序，请执行以下操作：
* 在开发环境中运行 `npm install ms-rest-azure`。
* 在开发环境中运行 `npm install azure-cognitiveservices-videosearch`。

## <a name="video-search-client"></a>视频搜索客户端
在“搜索”下获取[认知服务访问密钥](https://azure.microsoft.com/try/cognitive-services/)。 创建 `CognitiveServicesCredentials` 的实例：
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
然后对该客户端进行实例化：
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
搜索结果。
```
client.videosOperations.search('Interstellar Trailer').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>后续步骤

[认知服务 Node.js SDK 示例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
