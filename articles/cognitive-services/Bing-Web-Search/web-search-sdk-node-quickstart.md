---
title: 快速入门：使用用于 Node.js 的必应 Web 搜索 SDK
titleSuffix: Azure Cognitive Services
description: 可以使用必应 Web 搜索 SDK 轻松地将必应 Web 搜索集成到 Node.js 应用程序中。 本快速入门介绍如何实例化客户端、发送请求和输出响应。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 059862255ca2cf0ce435496bf22d866a37c8be71
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977159"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>快速入门：使用用于 Node.js 的必应 Web 搜索 SDK

可以使用必应 Web 搜索 SDK 轻松地将必应 Web 搜索集成到 Node.js 应用程序中。 本快速入门介绍如何实例化客户端、发送请求和输出响应。

想要马上查看代码？ GitHub 上提供了[适用于 Node.js 示例的必应 Web 搜索 SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)。

## <a name="prerequisites"></a>先决条件
下面是在开始本快速入门之前需要准备好的项目：

* [Node.js 6](https://nodejs.org/en/download/) 或更高版本
* 订阅密钥  

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>设置开发环境

让我们从设置 Node.js 项目的开发环境开始。

1. 为项目新建一个目录：

    ```console
    mkdir YOUR_PROJECT
    ```

1. 创建新的包文件：

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. 现在，让我们安装一些 Azure 模块并将它们添加到 `package.json`：

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>创建一个项目并声明必需的模块

在 `package.json` 所在的目录中，使用喜欢的 IDE 或编辑器新建一个 Node.js 项目。 例如：`sample.js`。

接下来，将以下代码复制到项目中。 它会加载在上一部分安装的模块。

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>对客户端进行实例化

以下代码实例化一个客户端并使用 `azure-cognitiveservices-websearch` 模块。 请确保为 Azure 帐户输入有效的订阅密钥，然后再继续。

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>发出请求并输出结果

使用客户端向必应 Web 搜索发送搜索查询。 如果响应包含 `properties` 数组中任何项的结果，则会将 `result.value` 输出到控制台。

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>运行该程序

最后一步是运行程序！

## <a name="clean-up-resources"></a>清理资源

完成本项目以后，请务必从程序代码中删除订阅密钥。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [认知服务 Node.js SDK 示例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>另请参阅

* [Azure Node SDK 参考](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/)
