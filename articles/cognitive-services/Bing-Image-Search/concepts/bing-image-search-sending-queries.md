---
title: 向必应图像搜索 API 发送查询 | Microsoft Docs
description: 了解如何将搜索查询发送到必应图像搜索 API，以及如何对其进行自定义。
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: d74f59ffcf095e639686a3ada3b09dac988fc544
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082571"
---
# <a name="sending-queries-to-the-bing-image-search-api"></a>向必应图像搜索 API 发送查询

必应图像搜索 API 通过允许向必应发送用户搜索查询并获取相关图像的列表，提供与 Bing.com/图像相似的体验。

## <a name="using-and-suggesting-search-terms"></a>使用和建议搜索词

输入搜索词以后，系统会在设置 [**q**](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) 查询参数之前对搜索词进行 URL 编码。 例如，如果用户输入 *sailing dinghies*，系统会将 `q` 设置为 `sailing+dinghies` 或 `sailing%20dinghies`。

如果应用有一个可以在其中输入搜索词的搜索框，则可使用[必应自动建议 API](../../bing-autosuggest/get-suggested-search-terms.md) 来实时显示建议的搜索词，以便改进体验。 此 API 根据部分搜索词和 Azure 认知服务返回建议的查询字符串。

## <a name="pivoting-the-query"></a>将查询分段

如果必应可以将原始搜索查询分段，则返回的 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 对象会包含 `pivotSuggestions`，后者可以作为可选搜索词显示给用户。 例如，如果原始查询为 *Microsoft Surface*，则必应可能会将查询分成 *Microsoft* 和 *Surface*，并为每个拆分项提供建议的分段。 这些可以作为可选查询词显示给用户。

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

`pivotSuggestions` 字段包含将原始查询分成的段的列表。 每个段的响应包含一个 [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) 对象的列表，该对象包含建议的查询。 `text` 字段包含建议的查询，`displayText` 字段包含的词替换原始查询中的段。 例如，“Surface 的发布日期”。

可以使用 `text` 和 `thumbnail` 字段向用户显示分段查询字符串（有可能分段查询字符串正是用户想要查找的）。 使用 `webSearchUrl` URL 或 `searchLink` URL，使缩略图和文本可供用户单击。 使用 `webSearchUrl` 向用户发送必应搜索结果，或者使用 `searchLink`（如果提供你自己的结果页）。

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>扩展查询

如果必应可以通过扩展查询来缩小原始搜索的范围，则 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 对象会包含 `queryExpansions` 字段。 例如，如果查询是 *Microsoft Surface*，则扩展的查询可以是 Microsoft Surface **Pro 3**、Microsoft Surface **RT**、Microsoft Surface **Phone** 和 Microsoft Surface **Hub**。

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

`queryExpansions` 字段包含 [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) 对象的列表。 `text` 字段包含扩展的查询，`displayText` 字段包含扩展词。 可以使用 `text` 和 `thumbnail` 字段向用户显示扩展的查询字符串（有可能扩展的查询字符串正是用户想要查找的）。 使用 `webSearchUrl` URL 或 `searchLink` URL，使缩略图和文本可供用户单击。 使用 `webSearchUrl` 向用户发送必应搜索结果，或者使用 `searchLink`（如果提供你自己的结果页）。

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>后续步骤

如果从未尝试过必应图像搜索 API，请尝试[快速入门](../quickstarts/csharp.md)。 如果需要更复杂的内容，请尝试关于如何创建[单页 Web 应用](../tutorial-bing-image-search-single-page-app.md)的教程。
