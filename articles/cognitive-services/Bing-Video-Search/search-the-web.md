---
title: 什么是必应视频搜索？
titlesuffix: Azure Cognitive Services
description: 演示如何使用必应视频搜索 API 在 Web 上搜索视频。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: overview
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: e48a0a056628e0c863330de792f8edfaa48aae34
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261853"
---
# <a name="what-is-bing-video-search"></a>什么是必应视频搜索？

必应视频搜索 API 提供的体验与[必应视频](https://www.bing.com/videos)类似（但并不完全相同）。 可以通过必应视频搜索 API 向必应发送搜索查询，取回相关视频的列表。

若要生成仅限视频的搜索结果页，以便查找与用户的搜索查询相关的视频，请调用此 API，而不是调用更常规的[必应 Web 搜索 API](../bing-web-search/search-the-web.md)。 如果需要视频和其他类型的内容，例如网页、新闻和图像，则请调用必应 Web 搜索 API。

## <a name="suggesting--using-search-terms"></a>建议和使用搜索词

如果提供供用户输入搜索词的搜索框，请使用[必应自动建议 API](../bing-autosuggest/get-suggested-search-terms.md) 来改进体验。 此 API 根据用户键入的部分搜索词返回建议的查询字符串。

用户输入搜索词以后，URL 在设置 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query) 查询参数之前会对搜索词进行编码。 例如，如果用户输入 sailing dinghies，系统会将 `q` 设置为 `sailing+dinghies` 或 `sailing%20dinghies`。

## <a name="getting-videos"></a>获取视频

若要从 Web 获取与用户的搜索词相关的视频，请发送以下 GET 请求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

所有请求必须从服务器发出。

如果是首次调用任何必应 API，请勿添加客户端 ID 请求头。 只有在以前调用过必应 API 且必应针对用户和设备组合返回了客户端 ID 的情况下，才包括客户端 ID。

若要从特定的域获取视频，请使用 [site:](https://msdn.microsoft.com/library/ff795613.aspx) 查询运算符。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

响应包含一个 [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) 应答，该应答包含必应认为与查询相关的视频的列表。 该列表中的每个 [Video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) 对象包含该视频的 URL、持续时间、尺寸和编码格式，以及其他属性。 视频对象还包含视频缩略图的 URL 以及缩略图的尺寸。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

可以显示包含所有视频缩略图的拼贴，也可以显示一部分缩略图。 如果显示一部分缩略图，请为用户提供查看剩余视频的选项。 必须按响应中提供的顺序显示视频。 若要了解如何重设缩略图的大小，请参阅[重设缩略图的大小并对其进行裁剪](./resize-and-crop-thumbnails.md)。 

当用户将鼠标悬停在缩略图上时，你可以使用 [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-motionthumbnailurl) 来播放视频的缩略图版本。 显示运动缩略图时，必须显示其属性。

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

如果用户单击缩略图，会显示以下视频观看选项：

- 使用 [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-hostpageurl) 可在托管网站（例如 YouTube）上观看视频
- 使用 [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-websearchurl) 可在必应视频浏览器中观看视频
- 使用 [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-embedhtml) 可将视频嵌入自己的体验中 

请确保在播放视频时使用发布者和创建者来设置视频的属性。

若要详细了解如何使用 [videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid) 来获取视频的见解，请参阅[视频见解](./video-insights.md)。

## <a name="filtering-videos"></a>筛选视频

默认情况下，视频搜索 API 返回与查询相关的所有视频。 如果只需免费视频或者时长不到五分钟的视频，请使用以下筛选器查询参数：

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#pricing)&mdash;按定价来筛选视频（例如，免费的视频或必须付费的视频）
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#resolution)&mdash;按分辨率来筛选视频（例如，分辨率为 720p 或更高的视频）
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videolength)&mdash;按视频长度来筛选视频（例如，长度不到五分钟的视频）
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#freshness)&mdash;按时间来筛选视频（例如，必应在过去一周发现的视频）

若要从特定域获取视频，请在查询字符串中包含 [site:](https://msdn.microsoft.com/library/ff795613.aspx) 查询运算符。

> [!NOTE]
> 如果使用 `site:` 查询运算符，则不管 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#safesearch) 设置如何，仍有可能出现响应中包含成人内容的情况，具体取决于查询。 只有在知道网站内容且方案允许使用成人内容的情况下，才应使用 `site:`。

以下示例演示了如何从 ContosoSailing.com 获取必应在过去一月发现的分辨率为 720p 或更高的免费视频。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>扩展查询

如果必应可以通过扩展查询来缩小原始搜索的范围，则 [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) 对象会包含 `queryExpansions` 字段。 例如，如果查询为 *Cleaning Gutters*，则扩展的查询可能为：Gutter Cleaning **Tools**、Cleaning Gutters **From the Ground**、Gutter Cleaning **Machine** 以及 **Easy** Gutter Cleaning。

以下示例演示了 *Cleaning Gutters* 的扩展查询。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

`queryExpansions` 字段包含 [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj) 对象的列表。 `text` 字段包含扩展的查询，`displayText` 字段包含扩展词。 可以使用文本和缩略图字段向用户显示扩展的查询字符串（有可能扩展的查询字符串正是用户想要查找的）。 使用 `webSearchUrl` URL 或 `searchLink` URL，使缩略图和文本可供用户单击。 使用 `webSearchUrl` 向用户发送必应搜索结果，或者使用 `searchLink`（如果提供你自己的结果页）。

## <a name="pivoting-the-query"></a>将查询分段

如果必应可以将原始搜索查询分段，则 [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) 对象会包含 `pivotSuggestions` 字段。 例如，如果原始查询为 *Cleaning Gutters*，则必应可能会将查询分成 *Cleaning* 和 *Gutters* 两段。

以下示例演示了针对 *Cleaning Gutters* 的分段建议。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

每个段的响应包含一个 [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj) 对象的列表，该对象包含建议的查询。 `text` 字段包含建议的查询，`displayText` 字段包含的词替换原始查询中的段。 例如，Window Cleaning。

可以使用 `text` 和 `thumbnail` 字段向用户显示扩展的查询字符串（有可能扩展的查询字符串正是用户想要查找的）。 使用 `webSearchUrl` URL 或 `searchLink` URL，使缩略图和文本可供用户单击。 使用 `webSearchUrl` 向用户发送必应搜索结果，或者使用 `searchLink`（如果提供你自己的结果页）。

## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>后续步骤

若要快速了解如何使用第一个请求，请参阅[发出第一个请求](./quick-start.md)。

若要获取订阅密钥，请参阅[订阅密钥](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api)。

自行熟悉[必应视频搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) 参考文档。 此参考文档包含可以用来请求搜索结果的终结点、标头和查询参数的列表。 此外还包含响应对象的定义。 

若要改进搜索框用户体验，请参阅[必应自动建议 API](../bing-autosuggest/get-suggested-search-terms.md)。 当用户输入其查询词时，你可以调用此 API，以便获取他人所使用的相关查询词。

确保阅读[必应使用和显示要求](./useanddisplayrequirements.md)，以免违反关于搜索结果使用要求的任何规则。

调用视频搜索 API 时，必应会返回结果的列表。 此列表是与查询相关的所有结果的一部分。 响应的 `totalEstimatedMatches` 字段包含一个估计数，是对可以查看的视频数的估计。 若要详细了解如何逐页查看剩余的视频，请参阅[分页视频](./paging-videos.md)。

若要详细了解如何获取视频见解，请参阅[视频见解](./video-insights.md)。

若要详细了解如何获取热门视频，请参阅[热门视频](./trending-videos.md)。