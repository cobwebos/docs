---
title: 自定义和建议图像搜索查询-必应图像搜索 API
titleSuffix: Azure Cognitive Services
description: 了解如何自定义发送到必应图像搜索 API 的搜索查询。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: d833b017004365e9dad7241e360f42ff41a55883
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542745"
---
# <a name="customize-and-suggest-image-search-queries"></a>自定义和建议图像搜索查询

使用本文章，了解如何自定义查询，并建议将发送到必应图像搜索 API 的搜索词。

## <a name="suggest-search-terms"></a>建议的搜索词

如果应用有一个可以在其中输入搜索词的搜索框，则可使用[必应自动推荐 API](../../bing-autosuggest/get-suggested-search-terms.md) 来改进体验。 该 API 可以实时显示建议的搜索词。 该 API 根据部分搜索词和认知服务返回建议的查询字符串。

## <a name="pivot-the-query"></a>分段查询

如果必应可以将原始搜索查询分段，则返回的 [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) 对象会包含 `pivotSuggestions`。 分段建议可以作为可选搜索词向用户显示。 例如，如果原始查询为 *Microsoft Surface*，则必应可能会将查询分成 *Microsoft* 和 *Surface*，并为每个拆分项提供建议的分段。 这些建议可以作为可选查询词向用户显示。

以下示例演示了针对 *Microsoft Surface* 的分段建议：  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

`pivotSuggestions` 字段包含将原始查询分成的段的列表。 每个段的响应包含一个 [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) 对象的列表，该对象包含建议的查询。 `text` 字段包含建议的查询。 `displayText` 字段包含替换原始查询中的分段的词。 例如，“Surface 的发布日期”。

如果分段查询字符串是用户要查找的内容，请使用 `text` 和 `thumbnail` 字段显示分段查询字符串。 使用 `webSearchUrl` URL 或 `searchLink` URL，使缩略图和文本可供用户单击。 使用 `webSearchUrl` 将用户发送到必应搜索结果。 如果提供自己的结果页面，请使用 `searchLink`。

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>扩展查询

如果必应可以通过扩展查询来缩小原始搜索的范围，则 [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) 对象会包含 `queryExpansions` 字段。 例如，如果查询为 *Microsoft Surface*，则扩展的查询可能为：
- Microsoft Surface **Pro 3**。
- Microsoft Surface **RT**。
- Microsoft Surface **Phone**。
- Microsoft Surface **Hub**。

以下示例演示了 *Microsoft Surface* 的扩展查询。

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

`queryExpansions` 字段包含 [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) 对象的列表。 `text` 字段包含扩展的查询。 `displayText` 字段包含扩展词。 如果扩展的查询字符串是用户要查找的内容，请使用 `text` 和 `thumbnail` 字段显示扩展的查询字符串。 使用 `webSearchUrl` URL 或 `searchLink` URL，使缩略图和文本可供用户单击。 使用 `webSearchUrl` 将用户发送到必应搜索结果。 如果提供自己的结果页面，请使用 `searchLink`。

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>后续步骤

如果从未尝试过必应图像搜索 API，请尝试[快速入门](../quickstarts/csharp.md)。 如果需要更复杂的内容，请尝试关于如何创建[单页 Web 应用](../tutorial-bing-image-search-single-page-app.md)的教程。
