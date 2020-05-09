---
title: 什么是必应实体搜索 API？
titleSuffix: Azure Cognitive Services
description: 使用必应实体搜索 API 从搜索查询中提取和搜索实体和场所。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2f374e29f4dc5406956cd56d1bb0bd1466e65773
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75384513"
---
# <a name="what-is-bing-entity-search-api"></a>什么是必应实体搜索 API？

必应实体搜索 API 向必应发送搜索查询，然后获得包含实体和场所的结果。 位置结果包括餐馆、酒店或其他当地企业。 如果查询指定了本地商业场所的名称或询问某类商业场所（例如，“我附近的餐馆”），则必应会返回场所。 如果查询指定知名的人物、场所（旅游景点、州、国家/地区等）或事物，则必应会返回实体。

|Feature  |说明  |
|---------|---------|
|[实时搜索建议](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | 当用户键入搜索词时，以下拉列表的形式提供搜索建议。       | 
| [实体歧义消除](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | 为具有多个可能含义的查询获取多个实体。 |
| [查找场所](concepts/search-for-entities.md#find-places) | 搜索并返回有关本地商家和实体的信息  |

## <a name="workflow"></a>工作流

必应实体搜索 API 是一项 RESTful Web 服务，可以轻松地通过任何编程语言调用，只要该语言能够发出 HTTP 请求和分析 JSON 即可。 可以通过 REST API 或 SDK 使用此服务。

1. 创建可以访问必应搜索 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有 Azure 订阅，可以免费[创建一个帐户](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。
2. 使用有效的搜索查询向 API 发送请求。
3. 通过分析返回的 JSON 消息处理 API 响应。

## <a name="next-steps"></a>后续步骤

* 尝试使用必应实体搜索 API 的[交互式演示](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/)。 
* 若要快速开始你的第一个请求，请尝试使用[快速入门](quickstarts/csharp.md)。
* [必应实体搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) 参考部分。
* [必应使用和显示要求](./use-display-requirements.md)指定了允许用户如何使用通过必应搜索 API 获得的内容和信息。
* 请访问[必应搜索 API 中心页](../bing-web-search/search-the-web.md)，浏览其他可用的 API。