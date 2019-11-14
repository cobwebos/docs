---
title: 必应实体搜索 API 终结点
titleSuffix: Azure Cognitive Services
description: 必应实体搜索 API 具有一个终结点，该终结点根据查询从 Web 返回实体。 这些搜索结果以 JSON 格式返回。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072673"
---
# <a name="bing-entity-search-api-endpoint"></a>必应实体搜索 API 终结点


必应实体搜索 API 具有一个终结点，该终结点根据查询从 Web 返回实体。 这些搜索结果以 JSON 格式返回。

## <a name="get-entity-results-from-the-endpoint"></a>从终结点获取实体结果

若要使用必应 API获取实体结果，请向以下终结点发送 `GET` 请求。 使用[标头](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers)和[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)来自定义搜索请求。 可以使用 `?q=` 参数发送搜索请求。

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [什么是必应实体搜索 API？](overview.md)

## <a name="see-also"></a>另请参阅 

有关标头、参数、市场代码、响应对象、错误等的详细信息，请参阅[必应实体搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) 参考文章。
