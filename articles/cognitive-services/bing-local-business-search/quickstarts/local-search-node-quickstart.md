---
title: 快速入门 - 使用 Node.js 将查询发送到 API - 必应当地企业搜索
titleSuffix: Azure Cognitive Services
description: 使用此快速入门开始向必应当地企业搜索 API 发送请求，该 API 是一项 Azure 认知服务。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: d0ee55c0baa6d7ec2ff86aa0e37b10ca4c973b77
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408129"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>快速入门：使用 Node.js 将查询发送到必应当地企业搜索 API

使用此快速入门了解如何向必应当地企业搜索 API 发送请求，该 API 是一项 Azure 认知服务。 尽管这个简单的应用程序是用 Node.js 编写的，但 API 是一种 RESTful Web 服务，可以与任何能够发出 HTTP 请求并解析 JSON 的编程语言兼容。

此示例应用程序从 API 获取搜索查询的本地响应数据。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 最新版本的 [Node.js](https://nodejs.org/en/download/)。
* [JavaScript 请求库](https://github.com/request/request)。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="创建必应搜索资源"  target="_blank">创建必应搜索资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>来获取密钥和终结点。 部署后，单击“转到资源”。


## <a name="code-scenario"></a>代码方案

下面的代码定义并发送请求，实现步骤如下：

1. 声明变量，以按主机和路径指定终结点。
2. 指定查询，然后添加查询参数。
3. 为响应创建处理程序函数。
4. 定义搜索函数，用以创建请求并添加 `Ocp-Apim-Subscription-Key` 标头。
5. 运行搜索函数。


```javascript
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

* [当地企业搜索 C# 快速入门](local-quickstart.md)
* [当地企业搜索 Java 快速入门](local-search-java-quickstart.md)
* [当地企业搜索 Python 快速入门](local-search-python-quickstart.md)
