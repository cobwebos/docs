---
title: 常见问题解答 (FAQ) - 必应自动推荐 API
titlesuffix: Azure Cognitive Services
description: 获取关于必应自动推荐 API 的常见问题解答。
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: b0ec10bbf03e8a8d005eece4b6496b74b2943233
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173252"
---
# <a name="frequently-asked-questions-faq-about-bing-autosuggest-api"></a>关于必应自动推荐 API 的常见问题解答 (FAQ)
 
 查找与 Azure 认知服务的自动建议 API 有关的概念、代码和方案相关的常见问题解答。

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>从 JavaScript 中调用必应自动建议 API 时，如何获取可选的客户端标头？

以下标头是可选的，但建议将其作为必需标头对待。 这些标头有助于必应自动建议 API 返回更准确的结果。

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

然而，当从 JavaScript 调用必应自动建议 API 时，浏览器内置的安全功能可能会阻止你访问这些标头的值。

若要解决此问题，可以通过 CORS 代理发出必应自动建议 API 请求。 来自此类代理的响应有一个 `Access-Control-Expose-Headers` 标头，此标头将响应标头列入允许列表，并将它们提供给 JavaScript。

可以轻松安装 CORS 代理，使我们的[教程应用](tutorials/autosuggest.md)可以访问可选的客户端标头。 首先，如果尚未安装 Node.js，请先[安装](https://nodejs.org/en/download/)。 然后在命令提示符处键入以下命令。

    npm install -g cors-proxy-server

接下来，在 HTML 文件中将必应自动建议 API 终结点更改为：

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

最后，使用以下命令启动 CORS 代理：

    cors-proxy-server

使用教程应用期间，不要关闭命令窗口；关闭窗口会导致代理停止运行。 在搜索结果下的可展开 HTTP 响应头部分中，现在可以看到 `X-MSEdge-ClientID` 响应头（以及其他响应头），并验证此响应头是否对所有请求都相同。

## <a name="next-steps"></a>后续步骤

问题是否与缺少功能相关？ 不妨在我们的 [用户之声网站](https://cognitive.uservoice.com/)上申请或投票支持相应功能。

## <a name="see-also"></a>另请参阅

- [堆栈溢出：认知服务](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
