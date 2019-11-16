---
title: 快速入门 - 使用 Node.js 将查询发送到必应当地企业搜索 API
titleSuffix: Azure Cognitive Services
description: 使用此快速入门开始向必应当地企业搜索 API 发送请求，该 API 是一项 Azure 认知服务。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: 925e3bfb4a89832d5cde7e35d5ce5e3be3a5bcbb
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075633"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>快速入门：使用 Node.js 将查询发送到必应当地企业搜索 API

使用此快速入门开始向必应当地企业搜索 API 发送请求，该 API 是一项 Azure 认知服务。 虽然这个简单的应用程序是用 Node.js 编写的，但 API 是一种 RESTful Web 服务，可以与任何能够发出 HTTP 请求并解析 JSON 的编程语言兼容。

此示例应用程序从 API 获取搜索查询 `hotel in Bellevue` 的本地响应数据。

## <a name="prerequisites"></a>先决条件

* 最新版本的 [Node.js](https://nodejs.org/en/download/)。

* [JavaScript 请求库](https://github.com/request/request)

必须拥有包含必应 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)足以满足本快速入门的要求。 使用免费试用版提供的访问密钥。  另请参阅[认知服务定价 - 必应搜索 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="code-scenario"></a>代码方案

以下代码定义并发送请求。 该代码通过以下步骤实现：

1. 声明变量，以按主机和路径指定终结点。
2. 指定查询，然后添加查询参数。
3. 为响应创建处理程序函数。
4. 定义搜索函数，用以创建请求并添加 Ocp-Apim-Subscription-Key 标头。
5. 运行“搜索”函数。

本演示的完整代码如下：

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>后续步骤

* [当地企业搜索快速入门](local-quickstart.md)
* [当地企业搜索 Java 快速入门](local-search-java-quickstart.md)
* [当地企业搜索 Python 快速入门](local-search-python-quickstart.md)
