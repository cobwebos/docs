---
title: 必应自动推荐终结点
titlesuffix: Azure Cognitive Services
description: 必应自动推荐 API 终结点摘要。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: c2d1c97ad2af266558f9b664162526d5006d2092
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830438"
---
# <a name="bing-autosuggest-endpoint"></a>必应自动推荐终结点

必应自动推荐 API 包含一个终结点，这将从部分搜索词返回建议的查询列表。

## <a name="endpoint"></a>终结点

若要使用必应 API 获取建议的查询，请向以下终结点发送 `GET` 请求。 使用标头和 URL 参数来定义更多规范。

终结点：将搜索建议作为 JSON 结果返回，该结果与 `?q=""` 定义的用户输入相关。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

有关标头、参数、市场代码、响应对象、错误等的详细信息，请参阅[必应自动建议 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) 参考。

## <a name="response-json"></a>响应 JSON

自动建议请求的响应包括作为 JSON 对象的结果。 有关分析结果的示例，请参阅[教程](tutorials/autosuggest.md)和[源代码](tutorials/autosuggest-source.md)。

## <a name="next-steps"></a>后续步骤

必应 API 支持根据其类型返回结果的搜索操作。 所有搜索终结点均将结果作为 JSON 响应对象返回。
所有终结点支持后列查询：按经度、纬度和搜索半径返回特定语言和/或位置的查询。

有关每个终结点支持的参数的完整信息，请参阅每个类型的参考页面。
有关使用自动建议 API 的基本请求的示例，请参阅[自动建议快速入门](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest)。