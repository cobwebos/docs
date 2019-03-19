---
title: 自动建议搜索词 - 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 将必应 Web 搜索 API 与必应自动推荐 API 配对，为用户提供增强的搜索体验。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: bf15ed704de305353d1ec141df6deb9d29b47f75
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339328"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>应用程序中的自动建议必应搜索词

如果提供供用户输入搜索词的搜索框，请使用[必应自动推荐 API](../bing-autosuggest/get-suggested-search-terms.md) 来改进体验。 此 API 根据用户键入的部分搜索词返回建议的查询字符串。

用户输入搜索词后，必须在设置 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query) 查询参数之前对其进行 URL 编码。 例如，如果用户输入 sailing dinghies，系统会将 `q` 设置为 `sailing+dinghies` 或 `sailing%20dinghies`。

如果查询词包含拼写错误，则搜索响应会包含 [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext) 对象。 该对象显示原始拼写以及必应用于搜索的已更正拼写。

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

可以使用此信息让用户了解你在显示搜索结果时修改了他们的查询字符串。

![查询上下文的用户体验示例](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>后续步骤  

* 查看示例[必应 Web 搜索 API 响应](search-responses.md)。  

## <a name="see-also"></a>另请参阅  

* [必应 Web 搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
