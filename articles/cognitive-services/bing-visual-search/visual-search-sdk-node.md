---
title: 快速入门：使用适用于 Node.js 的必应视觉搜索 SDK 获取图像见解
titleSuffix: Azure Cognitive Services
description: 使用必应视觉搜索 SDK 上传图像并获取关于它的见解。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: v-gedod
ms.openlocfilehash: 1dba0f3aacd0e51a9e4d8017a93f18928fd6b2ea
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744287"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>快速入门：使用适用于 Node.js 的必应视觉搜索 SDK 获取图像见解

根据本快速入门，开始使用 Node.js SDK 通过必应视觉搜索服务获取图像见解。 虽然必应视觉搜索具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js) 上找到此示例的源代码。 

## <a name="prerequisites"></a>先决条件
* [Node.js](https://www.nodejs.org/)
* 适用于 Node.js 的必应视觉搜索 SDK
    * 若要使用必应视觉搜索 SDK 来设置控制台应用程序，请运行以下命令：
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-search-visualSearch`。


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建新的 JavaScript 文件，然后添加以下要求。 针对订阅密钥、自定义配置 ID 和要上传的图像的文件路径创建变量。 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. 对客户端进行实例化。

    ```javascript
    let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);
    ```

## <a name="search-for-images"></a>搜索图像

1. 使用 `fs.createReadStream()` 读取图像文件，并针对搜索请求和结果创建变量。 然后使用客户端搜索图像。

    ```javascript
    let fileStream = fs.createReadStream(filePath);
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

2. 分析以前的查询的结果：

    ```javascript
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

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [构建单页 Web 应用](tutorial-bing-visual-search-single-page-app.md)
