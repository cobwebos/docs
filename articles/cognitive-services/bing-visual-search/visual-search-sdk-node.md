---
title: 快速入门：必应视觉搜索 SDK、Node
titleSuffix: Azure Cognitive Services
description: 设置视觉搜索 SDK Node 控制台应用程序。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: v-gedod
ms.openlocfilehash: afccbc66da94d5c2d276a05a47ffe60dfbae2261
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219696"
---
# <a name="quickstart-bing-visual-search-sdk-node"></a>快速入门：必应视觉搜索 SDK Node 

必应视觉搜索 SDK 将 REST API 功能用于 Web 请求和分析结果。
Git Hub 上提供 [Node 视觉搜索 SDK 示例的源代码](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js)。

代码方案记录在下列标题之下：
* [视觉搜索客户端](#client)
* [完整的控制台应用程序](#complete)

## <a name="prerequisites"></a>先决条件

* 必须有认知服务 API 密钥，才能验证 SDK 调用。 可以注册申请[免费试用密钥](https://azure.microsoft.com/try/cognitive-services/?api=search-api-v7)。 试用密钥有效期为七天且每秒调用一次。 对于生产方案，[请购买访问密钥](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7)。 另请参阅[定价信息](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/visual/)。

## <a name="application-dependencies"></a>应用程序依赖项

* 运行 `npm install Microsoft.Azure.CognitiveServices.Search.VisualSearch`。

<a name="client"></a>
## <a name="visual-search-client"></a>视觉搜索客户端
若要创建 `VisualSearchAPI` 客户端的实例，请添加 using 指令：
```
const Search = require('azure-cognitiveservices-visualsearch');
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;

```
然后对该客户端进行实例化：
```
let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
let credentials = new CognitiveServicesCredentials(keyVar);
let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);

```
使用客户端搜索图像：
```
let fileStream = fs.createReadStream('../Data/image.jpg');
let visualSearchRequest = JSON.stringify({});
let visualSearchResults;
try {
    visualSearchResults = await visualSearchApiClient.images.visualSearch({
        image: fileStream,
        knowledgeRequest: visualSearchRequest
    });
    console.log("Search visual search request with binary of image");
} catch (err) {
    console.log("Encountered exception. " + err.message);
}

```
分析以前的查询的结果：
```
// Visual Search results
if (visualSearchResults.image.imageInsightsToken) {
    console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
}
else {
    console.log("Couldn't find image insights token!");
}

// List of tags
if (visualSearchResults.tags.length > 0) {
    let firstTagResult = visualSearchResults.tags[0];
    console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

    // List of actions in first tag
    if (firstTagResult.actions.length > 0) {
        let firstActionResult = firstTagResult.actions[0];
        console.log(`First tag action count: ${firstTagResult.actions.length}`);
        console.log(`First tag action type: ${firstActionResult.actionType}`);
    }
    else {
        console.log("Couldn't find tag actions!");
    }

}
else {
    console.log("Couldn't find image tags!");
}

```
<a name="complete"></a>
## <a name="complete-console-application"></a>完成控制台应用程序

以下控制台应用程序执行以前的代码、其他方案并分析结果：
```
/*
 * Copyright (c) Microsoft Corporation. All rights reserved.
 * Licensed under the MIT License. See License.txt in the project root for
 * license information.
 */
'use strict';

const os = require("os");
const async = require('async');
const fs = require('fs');
const Search = require('azure-cognitiveservices-visualsearch');
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;


let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';

let credentials = new CognitiveServicesCredentials(keyVar);
let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);
let visualModels = visualSearchApiClient.models;
sample();

function sample() {
    async.series([
        async function () {
            let fileStream = fs.createReadStream('../Data/image.jpg');
            let visualSearchRequest = JSON.stringify({});
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    image: fileStream,
                    knowledgeRequest: visualSearchRequest
                });
                console.log("Visual search request with binary of dog image");
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
            if (!visualSearchResults) {
                console.log("No visual search result data. ");
            } else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                else {
                    console.log("Couldn't find image insights token!");
                }

                // List of tags
                if (visualSearchResults.tags.length > 0) {
                    let firstTagResult = visualSearchResults.tags[0];
                    console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                    // List of actions in first tag
                    if (firstTagResult.actions.length > 0) {
                        let firstActionResult = firstTagResult.actions[0];
                        console.log(`First tag action count: ${firstTagResult.actions.length}`);
                        console.log(`First tag action type: ${firstActionResult.actionType}`);
                    }
                    else {
                        console.log("Couldn't find tag actions!");
                    }

                }
                else {
                    console.log("Couldn't find image tags!");
                }
            }
        },
        async function () {
            let fileStream = fs.createReadStream('../Data/image.jpg');
            let cropArea = { top: 0.1, bottom: 0.5, left: 0.1, right: 0.9 };
            let imageInfo = { cropArea: cropArea };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    image: fileStream,
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nVisual search request with binary of image with JSON info of cropArea");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
                            console.log(`First tag action count: ${firstTagResult.actions.length}`);
                            console.log(`First tag action type: ${firstActionResult.actionType}`);
                        }
                        else {
                            console.log("Couldn't find tag actions!");
                        }

                    }
                    else {
                        console.log("Couldn't find image tags!");
                    }
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        async function () {
            let imageInfo = { url: "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80" };
            let filters = { site: "www.bing.com" };
            let knowledgeRequest = { filters: filters };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo, knowledgeRequest: knowledgeRequest });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nSearch visual search request with imageInfo and filters in JSON text");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
                            console.log(`First tag action count: ${firstTagResult.actions.length}`);
                            console.log(`First tag action type: ${firstActionResult.actionType}`);
                        }
                        else {
                            console.log("Couldn't find tag actions!");
                        }

                    }
                    else {
                        console.log("Couldn't find image tags!");
                    }
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        async function () {
            var imageInsightsToken = "bcid_113F29C079F18F385732D8046EC80145*ccid_oV/QcH95*mid_687689FAFA449B35BC11A1AE6CEAB6F9A9B53708*thid_R.113F29C079F18F385732D8046EC80145";
            let cropArea = { top: 0.1, bottom: 0.5, left: 0.1, right: 0.9 };
            let imageInfo = { imageInsightsToken: imageInsightsToken, cropArea: cropArea };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nVisual search request with ImageInsightsToken and crop area of image");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
                            console.log(`First tag action count: ${firstTagResult.actions.length}`);
                            console.log(`First tag action type: ${firstActionResult.actionType}`);
                        }
                        else {
                            console.log("Couldn't find tag actions!");
                        }

                    }
                    else {
                        console.log("Couldn't find image tags!");
                    }
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        function () {
            return new Promise((resolve) => {
                console.log(os.EOL);
                console.log("Finished running Visual-Search sample.");
                resolve();
            })
        }
    ], (err) => {
        throw (err);
        });
}

exports.sample = sample;
```

## <a name="next-steps"></a>后续步骤

[认知服务 .NET SDK 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)。
