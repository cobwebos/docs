---
title: 什么是必应视觉搜索？
titleSuffix: Azure Cognitive Services
description: 必应视觉搜索提供与图像相关的详细信息或见解，如类似图像或购物源。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 61a851b0efbcc4fdb55308e47447d218014ef9e0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154348"
---
# <a name="what-is-the-bing-visual-search-api"></a>什么是必应视觉搜索 API？

必应视觉搜索 API 提供类似于 Bing.com/images 上所示的图像细节。 上传图像或提供图像的 URL 后，此 API 可以识别该图像的各种细节，包括外观相似的图像、购物源、包含图像的网页，等等。 如果使用[必应图像搜索 API](../bing-image-search/overview.md)，则可以使用附加到 API 搜索结果的见解标记，而无需上传图像。

## <a name="insights"></a>洞察力

下面是视觉搜索提供的见解：

| 见解                              | 说明 |
|--------------------------------------|-------------|
| 视觉上相似的图像              | 与输入图像在外观上相似的图像列表。 |
| 外观相似的产品            | 与所示产品外观相似的产品。            |
| 购物源                     | 可以购买输入图像中所示项的位置。            |
| 相关搜索                     | 其他人执行的相关搜索或基于图像内容的相关搜索。            |
| 包含图像的网页     | 包含输入图像的网页。            |
| 食谱                              | 包含输入图像中所示食物的食谱的网页            |

除了这些见解，视觉搜索还可以返回一组派生自输入图像的不同术语（标记）。 这些标记允许用户浏览在图像中找到的概念。 例如，如果输入图像是一位著名运动员，其中一个标记可以是运动员的姓名，另一个标记可以是体育。 或者，如果输入图像是一个苹果馅饼，标记可以是苹果馅饼、馅饼、甜点，以便用户可以浏览相关概念。

视觉搜索结果还包含图像中感兴趣区域对应的边框。 例如，如果图像包含多个名人，结果可能包含图像中每个已识别名人对应的边框。 或者，如果必应识别图像中的产品或服装，结果可能包含已识别产品或服装项对应的边框。

> [!IMPORTANT]
> 如果使用必应图像搜索 API 获取图像见解，请考虑切换到必应视觉搜索 API，因为它可以提供更全面的见解。

## <a name="workflow"></a>工作流

必应视觉搜索 API 是一项 RESTful Web 服务，可以轻松地通过任何编程语言调用，只要该语言能够发出 HTTP 请求和分析 JSON 即可。 可以通过 REST API 或 SDK 使用此服务。

1. 创建可以访问必应搜索 API 的认知服务 API 帐户。 如果没有 Azure 订阅，可以免费创建一个帐户。
2. 使用有效的搜索查询向 API 发送请求。
3. 通过分析返回的 JSON 消息处理 API 响应。


## <a name="next-steps"></a>后续步骤

首先，请尝试必应搜索 API [交互式演示](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)。
此演示介绍如何快速自定义搜索查询并在 Web 中搜索图像。

做好调用 API 的准备后，请创建一个[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有 Azure 订阅，可以免费[创建一个帐户](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。

若要快速了解如何使用第一个请求，请参阅快速入门：[C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)。


## <a name="see-also"></a>另请参阅

* [必应视觉搜索参考](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)文档包含有关终结点、标头、API 响应和查询参数的定义和信息，这些都可以用来请求基于图像的搜索结果。

* [必应使用和显示要求](./use-and-display-requirements.md)指定了允许用户如何使用通过必应搜索 API 获得的内容和信息。
