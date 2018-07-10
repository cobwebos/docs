---
title: 项目 URL 预览终结点 - Microsoft 认知服务 | Microsoft Docs
description: URL 预览终结点的摘要。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: ddd53aa49db01d7a6db397eb285d0854edc59388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366266"
---
# <a name="project-url-preview-endpoint"></a>项目 URL 预览终结点

URL 预览 API 包括一个终结点。

## <a name="endpoint"></a>终结点
若要获取 URL 预览，请向以下终结点发送请求。 使用标头和 URL 参数进行其他指定。

获取：
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

````

### <a name="query-parameters"></a>查询参数
|名称|值|类型|必需|  
|----------|-----------|----------|--------------|  
|q|要预览的 URL|字符串 |是|
|safeSearch|非法的成人内容或盗版内容将被阻止且系统会显示错误代码 400，并且不会返回 *isFamilyFriendly* 标志。 <p>对于合法的成人内容，行为如下。 状态代码返回 200，并且 *isFamilyFriendly* 标志设置为 false。<ul><li>safeSearch=strict：将不会返回标题、说明、URL 和图像。</li><li>safeSearch=moderate：将获取标题、URL 和说明，但不获取说明性图像。</li><li>safeSearch=off：获取所有响应对象/元素 – 标题、URL、说明和图像。</li></ul> |字符串|非必需。 </br> 默认为 safeSearch=strict。| 

## <a name="response-object"></a>响应对象

响应包括 HTTP 标头和 WebPage 对象，该对象具有如下例所示的属性：`name`、`url`、`description`、`isFamilyFriendly` 和 `primaryImageOfPage`。

````
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

````

## <a name="next-steps"></a>后续步骤
- [C# 快速入门](csharp.md)
- [Java 快速入门](java-quickstart.md)
- [JavaScript 快速入门](javascript.md)
- [Node 快速入门](node-quickstart.md)
- [Python 快速入门](python-quickstart.md)
