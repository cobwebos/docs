---
title: Azure 上必应 Web 搜索 API 的常见问题解答 (FAQ) | Microsoft Docs
description: 获取 Azure 上 Microsoft 认知服务必应 Web 搜索 API 的常见问题解答。
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 321f571c48f2231d1ced43848cdefd17adaa1a08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365491"
---
# <a name="frequently-asked-questions-faq-about-bing-web-search-api-cognitive-services"></a>必应 Web 搜索 API（认知服务）常见问题解答 (FAQ)
 
 获取与 Azure 上 Microsoft 认知服务必应 Web 搜索 API 有关的概念、代码和方案的常见问题解答。

## <a name="response-headers-in-javascript"></a>JavaScript 响应头

下面列出了必应 Web 搜索 API 的可能响应头。

|||
|-|-|
|`X-MSEdge-ClientID`|必应分配给用户的唯一 ID|
|`BingAPIs-Market`|用于完成请求的市场|
|`BingAPIs-TraceId`|此请求在必应 API 服务器上的日志项目（用于客户支持）|

请务必保留客户端 ID，并在后续请求中返回它。 如果这样做，搜索不仅会在排名搜索结果时使用过往上下文，还会提供一致用户体验。

不过，通过 JavaScript 调用必应 Web 搜索 API 时，浏览器内置的安全功能 (CORS) 可能会阻止访问这些响应头的值。

若要访问响应头，可以通过 CORS 代理发出必应 Web 搜索 API 请求。 此类代理的响应中有 `Access-Control-Expose-Headers` 头，可以将响应头列入白名单，让响应头可供 JavaScript 访问。

CORS 代理安装起来很简单，可便于[教程应用](tutorial-bing-web-search-single-page-app.md)访问可选的客户端响应头。 首先，如果尚未安装 Node.js，请先[安装它](https://nodejs.org/en/download/)。 然后，在命令提示符处输入以下命令。

    npm install -g cors-proxy-server

接下来，将 HTML 文件中的必应 Web 搜索 API 终结点更改为：

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

最后，运行下面的命令，启动 CORS 代理：

    cors-proxy-server

使用教程应用期间，不要关闭命令窗口；关闭窗口会导致代理停止运行。 在搜索结果下的可展开 HTTP 响应头部分中，现在可以看到 `X-MSEdge-ClientID` 响应头（以及其他响应头），并验证此响应头是否对所有请求都相同。

## <a name="response-headers-in-production"></a>生产环境中的响应头

上一常见问题解答中介绍的 CORS 代理方法适合于开发、测试和学习环境。 

不过，在生产环境中，应在使用必应 Web 搜索 API 的网页所处的域中托管服务器端脚本。 此脚本实际应在网页 JavaScript 发出请求后执行 API 调用，并将所有结果（包括响应头）返回给客户端。 由于（页面和脚本）这两个资源共用源，因此 CORS 不起作用，网页 JavaScript 可以访问特殊响应头。 

这种方法还可以保护 API 密钥免遭公开，因为只有服务器端脚本需要它。 此脚本可以使用其他方法，以确保对请求进行授权。

## <a name="next-steps"></a>后续步骤

问题是否与缺少功能相关？ 不妨在我们的 [UserVoice 网站](https://cognitive.uservoice.com/forums/555907-bing-search)上申请或投票支持相应功能。

## <a name="see-also"></a>另请参阅

 [Stack Overflow：认知服务](http://stackoverflow.com/questions/tagged/bing-api)