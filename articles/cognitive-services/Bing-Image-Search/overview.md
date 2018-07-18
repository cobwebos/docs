---
title: 什么是必应图像搜索？ | Microsoft Docs
description: 了解如何使用必应图像搜索 API 搜索 Web 中的图像。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336513"
---
# <a name="what-is-bing-image-search"></a>什么是必应图像搜索？

必应图像搜索 API 提供的体验类似于[必应图像](https://www.bing.com/images)，都是让你向必应发送用户搜索查询，然后就会返回相关图像的列表。

若要生成仅限图像的搜索结果页，以便查找与用户的搜索查询相关的图像，请调用此 API，而不是调用更常规的 [Web 搜索 API](../bing-web-search/search-the-web.md)。 如果需要图像和其他类型的内容，例如网页、新闻和视频，则请调用 Web 搜索 API。

## <a name="suggesting--using-search-terms"></a>建议和使用搜索词

如果提供供用户输入搜索词的搜索框，请使用[必应自动建议 API](../bing-autosuggest/get-suggested-search-terms.md) 来改进体验。 此 API 根据用户键入的部分搜索词返回建议的查询字符串。

用户输入搜索词以后，URL 在设置 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) 查询参数之前会对搜索词进行编码。 例如，如果用户输入 *sailing dinghies*，系统会将 `q` 设置为 `sailing+dinghies` 或 `sailing%20dinghies`。

## <a name="getting-images"></a>获取图像

若要从 Web 获取与用户的搜索词相关的图像，请发送以下 GET 请求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

所有请求必须从服务器发出。 不能从客户端进行调用。

如果是第一次调用任何必应 API，请勿包括客户端 ID 标头。 只有在以前调用过必应 API 且必应针对用户和设备组合返回了客户端 ID 的返回情况下，才包括客户端 ID。

若要从特定的域获取图像，请使用 [site:](http://msdn.microsoft.com/library/ff795613.aspx) 查询运算符。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

响应包含一个 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 应答，该应答包含必应认为与查询相关的图像的列表。 列表中的每个 [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) 对象包含图像的 URL、大小、尺寸和编码格式。 图像对象还包含图像的缩略图的 URL 以及缩略图的尺寸。

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

可以显示包含所有图像缩略图的拼贴，也可以显示一部分缩略图。 如果显示一部分缩略图，请为用户提供查看剩余图像的选项。 必须按响应中提供的顺序显示图像。

也可以在用户将光标悬停在缩略图上方时展开缩略图。 确保在展开图像时标识图像的归属。 例如，可以从 [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) 提取主机，然后将其显示在图像下方。 若要了解如何重设缩略图的大小，请参阅[重设缩略图的大小并对其进行裁剪](./resize-and-crop-thumbnails.md)。

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

如果用户单击缩略图，则可使用 [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) 向用户显示全尺寸图像。 确保标识图像的归属。

如果 `shoppingSourcesCount` 或 `recipeSourcesCount` 大于零，请向缩略图添加标记（例如购物车），说明图像中存在商品或食品。

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

若要获取图像的见解，例如包含图像的网页或在图像中识别的人物，请使用 [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken)。 有关详细信息，请参阅[图像见解](./image-insights.md)。

## <a name="filtering-images"></a>筛选图像

 默认情况下，图像搜索 API 返回与查询相关的所有图像。 但是，如果只需背景透明的图像或特定大小的图像，则可使用以下查询参数来筛选必应返回的图像。  

- [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) - 按纵横比筛选图像（例如，标准图像或宽屏图像）
- [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) - 按主色或黑白色筛选图像
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) - 按时间筛选图像（例如，必应在过去一周发现的图像）
- [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height)、[width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) - 按高度和宽度筛选图像
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) - 按内容筛选图像（例如，只显示人的面孔的图像）
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) - 按类型筛选图像（例如，剪贴画、动画 GIF 或透明背景图像）
- [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) - 按与站点关联的许可证类型筛选图像
- [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) - 按大小筛选图像（例如，最大尺寸为 200x200 像素的小图像）

若要从特定的域获取图像，请使用 [site:](http://msdn.microsoft.com/library/ff795613.aspx) 查询运算符。

> [!NOTE]
> 如果使用 `site:` 运算符，则不管 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) 设置如何，仍有可能出现响应中包含成人内容的情况，具体取决于查询。 只有在知道网站内容且方案允许使用成人内容的情况下，才应使用 `site:`。

以下示例演示了如何从 ContosoSailing.com 获取必应在过去一周发现的小图像。  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>将查询分段

如果必应可以将原始搜索查询分段，则 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 对象会包含 `pivotSuggestions` 字段。 例如，如果原始查询为 *Microsoft Surface*，则必应可能会将查询分成 *Microsoft* 和 *Surface* 两段。  

以下示例演示了针对 *Microsoft Surface* 的分段建议。  

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

然后即可向用户显示可选的查询词（如果用户对其感兴趣）。

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

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>后续步骤

若要快速了解如何使用第一个请求，请参阅 [C# 快速入门](quickstarts/csharp.md)。

自行熟悉[必应图像搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) 参考文档。 此参考文档包含可以用来请求搜索结果的终结点、标头和查询参数的列表， 此外还包含响应对象的定义。

若要改进搜索框用户体验，请参阅[必应自动建议 API](../bing-autosuggest/get-suggested-search-terms.md)。 当用户输入其查询词时，你可以调用此 API，以便获取他人所使用的相关查询词。

确保阅读[必应使用和显示要求](./useanddisplayrequirements.md)，以免违反关于搜索结果使用要求的任何规则。

调用必应图像搜索 API 时，必应会返回结果的列表。 此列表是与查询相关的所有结果的一部分。 响应的 `totalEstimatedMatches` 字段包含一个估计数，是对可以查看的图像数的估计。 若要详细了解如何逐页查看剩余的图像，请参阅[分页图像](./paging-images.md)。