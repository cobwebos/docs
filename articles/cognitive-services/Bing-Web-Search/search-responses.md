---
title: 必应 Web 搜索 API 响应 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 了解必应 Web 搜索 API 提供的应答类型和响应。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/13/2018
ms.author: erhopf
ms.openlocfilehash: 13e9792f3d5765047dabb4cdef59e85a47a69aba
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889384"
---
# <a name="bing-web-search-responses"></a>必应 Web 搜索响应  

向必应 Web 搜索发送搜索请求时，它会在响应正文中返回一个 [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) 对象。 针对必应确定与查询相关的每个应答，该对象包含一个字段。 本示例演示当必应返回所有应答时的响应对象：

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

通常，必应 Web 搜索返回一部分应答。 例如，如果查询字词是 *sailing dinghies*，则响应可能包含 `webPages`、`images` 和 `rankingResponse`。 除非使用了 [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter) 来筛选出网页，否则响应始终包含 `webpages` 和 `rankingResponse` 应答。

## <a name="webpages-answer"></a>网页应答

[webPages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) 应答包含必应 Web 搜索确定与查询相关的网页的链接列表。 该列表中的每个[网页](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webpage)包含该页面的名称、URL、显示 URL、内容简短说明，以及必应找到内容的日期。

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

使用 `name` 和 `url` 可以创建一个超链接用于将用户转到该网页。

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Images 响应

[images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 应答包含必应认为与查询相关的图像列表。 该列表中的每个[图像](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image)包含该图像的 URL、大小、尺寸和编码格式。 图像对象还包含图像的缩略图的 URL 以及缩略图的尺寸。

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

根据用户的设备，通常会显示一部分缩略图，以及可让用户查看剩余图像的选项。

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

也可以在用户将光标悬停在缩略图上方时展开缩略图。 确保在展开图像时标识图像的归属。 例如，可以从 `hostPageDisplayUrl` 提取主机，然后将其显示在图像下方。 若要了解如何重设缩略图的大小，请参阅[重设缩略图的大小并对其进行裁剪](./resize-and-crop-thumbnails.md)。

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

如果用户单击缩略图，可以使用 `webSearchUrl` 将用户转到必应的图像搜索结果页，其中包含图像的拼贴图。

有关图像应答和图像的详细信息，请参阅[必应图像搜索 API](../bing-image-search/search-the-web.md)。

## <a name="related-searches-answer"></a>相关搜索应答

[relatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse-relatedsearches) 应答包含其他用户最经常执行的相关查询列表。 该列表中的每个[查询](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query_obj)包含一个查询字符串 (`text`)、突出显示了匹配项字符的查询字符串 (`displayText`)，以及该查询的必应搜索结果页的 URL (`webSearchUrl`)。

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

使用 `displayText` 查询字符串和 `webSearchUrl`URL 可以创建一个超链接，用于将用户转到相关查询的必应搜索结果页。 也可以在自己的 Web 搜索 API 查询中使用 `text` 查询字符串，并自行显示结果。

有关如何处理 `displayText` 中突出显示的标记的信息，请参阅[匹配项突出显示](./hit-highlighting.md)。

下面演示了在 Bing.com 中使用相关查询的示例。

![必应中的相关搜索示例](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Videos 应答

[videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) 应答包含必应认为与查询相关的视频列表。 该列表中的每个[视频](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video)包含该视频的 URL、持续时间、尺寸和编码格式。 视频对象还包含视频缩略图的 URL 以及缩略图的尺寸。

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

根据用户的设备，通常会显示一部分视频，以及可让用户查看剩余视频的选项。 将显示视频的缩略图以及长度、说明（名称）和属性（发布者）。

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

当用户将鼠标悬停在缩略图上时，你可以使用 `motionThumbnailUrl` 来播放视频的缩略图版本。 显示运动缩略图时，必须显示其属性。

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

如果用户单击缩略图，会显示以下视频观看选项：

- 使用 `hostPageUrl` 可在宿主网站（例如 YouTube）上观看视频
- 使用 `webSearchUrl` 可在必应视频浏览器中观看视频
- 使用 `embedHtml` 可将视频嵌入自己的体验

有关视频应答和视频的详细信息，请参阅[必应视频搜索 API](../bing-video-search/search-the-web.md)。

## <a name="news-answer"></a>News 应答

[news](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) 应答包含必应认为与查询相关的新闻列表。 该列表中的每篇[新闻](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle)包含该文章的名称、说明及其在宿主网站上的 URL。 如果文章包含图像，则该对象包括图像的缩略图。

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

根据用户的设备，会显示一部分新闻，以及可让用户查看剩余新闻的选项。 使用 `name` 和 `url` 可以创建一个超链接，用于将用户转到宿主站点上的新闻。 如果文章包含图像，请使用 `url` 为图像嵌入可单击的链接。 请务必使用 `provider` 来指定文章的属性。

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

有关 news 应答和新闻的详细信息，请参阅[必应新闻搜索 API](../bing-news-search/search-the-web.md)。

## <a name="computation-answer"></a>Computation 应答

如果用户输入数学表达式或单位转换查询，响应可能包含 [Computation](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#computation) 应答。 `computation` 应答包含规范化表达式及其结果。

单位转换查询是将一个单位转换为另一个单位的查询。 例如，“10 米是多少英尺？”或“1/4 杯有多少汤匙？”

下面显示了“10 米是多少英尺？”的 `computation` 应答

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

下面显示了数学查询及其相应 `computation` 应答的示例。

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

数学表达式可包含以下符号：

|符号|Description|
|------------|-----------------|
|+|加|
|-|减|
|/|除|
|*|乘|
|^|幂|
|!|阶乘|
|。|小数|
|()|优先级分组|
|[]|函数|

数学表达式可包含以下常量：

|符号|Description|
|------------|-----------------|
|Pi|3.14159...|
|Degree|度|
|i|虚数|
|e|e，2.71828...|
|GoldenRatio|黄金比率，1.61803...|

数学表达式可包含以下函数：

|符号|Description|
|------------|-----------------|
|Sqrt|平方根|
|Sin[x]、Cos[x]、Tan[x]<br />Csc[x]、Sec[x]、Cot[x]|三角函数（以弧度为单位的参数）|
|ArcSin[x]、ArcCos[x]、ArcTan[x]<br />ArcCsc[x]、ArcSec[x]、ArcCot[x]|反三角函数（以弧度为单位给出结果）|
|Exp[x]、E^x|指数函数|
|Log[x]|自然对数|
|Sinh[x]、Cosh[x]、Tanh[x]<br />Csch[x]、Sech[x]、Coth[x]|双曲函数|
|ArcSinh[x]、ArcCosh[x]、ArcTanh[x]<br />ArcCsch[x]、ArcSech[x]、ArcCoth[x]|反双曲函数|

不支持包含变量的数学表达式（例如，4x+6=18，其中的 x 是变量）。

## <a name="timezone-answer"></a>TimeZone 应答

如果用户输入时间或日期查询，响应可能包含 [TimeZone](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#timezone) 应答。 此应答支持隐式或显式查询。 隐式查询（例如，“现在是几点”）基于用户的位置返回本地时间。 显式查询（例如，“西雅图现在是几点”）返回华盛顿州西雅图的本地时间。

`timeZone` 应答提供位置的名称、指定位置的当前 UTC 日期和时间，以及 UTC 时差。 如果位置边界处于多个时区内，则该应答包含该边界内所有时区的当前 UTC 日期和时间。 例如，由于佛罗里达州跨两个时区，因此该应答包含这两个时区的本地日期和时间。  

如果查询请求某个州/省或国家/地区的时间，必应将确定位置地理边界内的主要城市，并在 `primaryCityTime` 字段中返回该城市。 如果边界包含多个时区，则在 `otherCityTimes` 字段中返回剩余的时区。

下面显示了返回 `timeZone` 应答的示例查询。

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>SpellSuggestion 应答

如果必应判断用户的意图可能搜索不同的内容，则响应中会包含 [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#spellsuggestions) 对象。 例如，如果用户搜索 *carlos pen*，必应会判断该用户的意图可能是搜索 Carlos Pena（基于以往其他用户的搜索来判断），而不是 *carlos pen*。 下面显示了示例拼写响应。

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

下面显示了必应如何使用拼写建议。

![必应拼写建议示例](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>后续步骤  

* 查看[请求限制](throttling-requests.md)文档。  

## <a name="see-also"></a>另请参阅  

* [必应 Web 搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
