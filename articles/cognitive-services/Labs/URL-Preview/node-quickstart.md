---
title: 适用于项目 URL 预览的 Node.js 快速入门 - Microsoft 认知服务 | Microsoft Docs
description: 开始使用 Azure 上的 Microsoft 认知服务中的 URL 预览。
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 195033d2740b11873baae095cec028dc8d19ce49
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366271"
---
# <a name="url-preview-node-quickstart"></a>URL 预览 Node 快速入门

以下 Node 示例创建 SwiftKey 网站的 URL 预览：https://swiftkey.com/en。

## <a name="prerequisites"></a>先决条件

获取[认知服务实验室](https://aka.ms/answersearchsubscription)免费试用版的访问密钥

## <a name="code-scenario"></a>代码方案 

下面的代码获取 URL 预览数据。
它是通过以下步骤实现的：
1. 声明变量来通过主机和路径指定终结点。
2. 指定要预览的查询 URL，然后添加查询参数。  
3. 为响应创建处理程序函数。
4. 定义搜索函数，用以创建请求并添加 *Ocp-Apim-Subscription-Key* 标头。
5. 运行“搜索”函数。 

本演示的完整代码如下：

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-ACCESS-KEY'; 
let host = 'api.labs.cognitive.microsoft.com';
let path = '/urlpreview/v7.0/search';

let mkt = 'en-US';
let q = 'https://swiftkey.com/';

let params = '?q=' + encodeURI(q);

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

````

## <a name="next-steps"></a>后续步骤
- [C# 示例代码](csharp.md)
- [Java 快速入门](java-quickstart.md)
- [JavaScript 快速入门](javascript.md)
- [Python 快速入门](python-quickstart.md)