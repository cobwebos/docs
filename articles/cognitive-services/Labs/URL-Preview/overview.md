---
title: 什么是项目 URL 预览？ - Microsoft 认知服务 | Microsoft Docs
description: 项目 URL 预览简介。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6b486e0ab4092bef4fe829a5f166311a572a2900
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366263"
---
# <a name="what-is-project-url-preview"></a>什么是项目 URL 预览？
URL 预览终结点采用 URL 查询参数并返回 JSON 响应，其中包含目标资源的名称、简要说明以及要在预览中显示的图像的链接。 响应还包含 [isFamilyFriendly](url-preview-reference.md#query-parameters) 标志，指示 URL 是否包含成人、盗版或其他非法内容。 

若要获取 URL 预览结果，请提交 GET 请求，并在 Ocp-Apim-Subscription-Key 标头中包含有效令牌：  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
响应： 
````
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

````
## <a name="scenarios"></a>方案 

URL 预览 API 支持 Web 资源的简要描述。 开发人员可使用其构建丰富的预览体验。  用户可共享网页、新闻、博客、论坛等或为其添加书签。此 API 还可用于内容审核。    

应用程序使用 URL 预览将 Web 请求发送到终结点，并将一个查询分配给要预览的 URL。  JSON 响应包含后列预览信息：名称、资源描述、familyFriendly 标志以及链接，通过这些链接可在线访问代表性图像和完整资源。 

## <a name="terms-of-use"></a>使用条款
仅使用项目 URL 预览中的数据，在社交媒体、聊天机器人或类似产品上共享的由最终用户启动的 URL 中显示使用超链接链接到其源站点的预览代码片段和缩略图图像。 不得复制、存储或缓存从项目 URL 预览收到的任何数据。 需执行后列操作请求：禁用可能从网站或内容所有者处收到的预览。

你或代表你的第三方不得出于测试、开发、训练、分发或提供任何非 Microsoft 服务或功能的目的使用、保留、存储、缓存、共享或分发来自 URL 预览 API 的任何数据。 

## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>后续步骤
- [C# 快速入门](csharp.md)
- [Java 快速入门](java-quickstart.md)
- [JavaScript 快速入门](javascript.md)
- [Node 快速入门](node-quickstart.md)
- [Python 快速入门](python-quickstart.md)
