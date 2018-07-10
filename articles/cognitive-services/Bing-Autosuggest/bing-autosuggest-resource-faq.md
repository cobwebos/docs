---
title: 有关 Azure 自动建议 API 的常见问题解答 (FAQ) | Microsoft Docs
description: 获取有关 Azure 上的 Azure 认知服务自动建议 API 的常见问题解答。
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 00b91728bcfec52ff30697f080d5c2619bab79a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366425"
---
# <a name="frequently-asked-questions-faq-about-autosuggest-api-cognitive-services"></a>有关自动建议 API（认知服务）的常见问题解答 (FAQ)
 
 查找与 Azure 认知服务的自动建议 API 有关的概念、代码和方案相关的常见问题解答。

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>从 JavaScript 中调用必应自动建议 API 时，如何获取可选的客户端标头？

以下标头是可选的，但建议将其作为必需标头对待。 这些标头有助于必应自动建议 API 返回更准确的结果。

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

然而，当从 JavaScript 调用必应自动建议 API 时，浏览器内置的安全功能可能会阻止你访问这些标头的值。

若要解决此问题，可以通过 CORS 代理发出必应自动建议 API 请求。 来自此类代理的响应有一个 `Access-Control-Expose-Headers` 标头，此标头将响应标头列入允许列表，并将它们提供给 JavaScript。

可以轻松安装 CORS 代理，使我们的[教程应用](tutorials/autosuggest.md)可以访问可选的客户端标头。 首先，如果尚未安装 Node.js，请[安装它](https://nodejs.org/en/download/)。 然后在命令提示符处键入以下命令。

    npm install -g cors-proxy-server

接下来，在 HTML 文件中将必应自动建议 API 终结点更改为：

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

最后，使用以下命令启动 CORS 代理：

    cors-proxy-server

在使用教程应用期间，请让命令窗口保持打开状态；关闭此窗口会阻止代理。 在搜索结果下的可展开 HTTP 标头部分中，现在可以看到 `X-MSEdge-ClientID` 标头（以及其他标头），并可验证每个请求的该标头是否都相同。

## <a name="next-steps"></a>后续步骤

问题是否与缺少功能相关？ 请考虑在我们的[用户之声网站](https://cognitive.uservoice.com/)上发起请求或投票。

## <a name="see-also"></a>另请参阅

- [堆栈溢出：认知服务](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
