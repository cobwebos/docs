---
title: 使用必应新闻搜索 API 搜索新闻
titlesuffix: Azure Cognitive Services
description: 了解如何发送针对一般新闻、热门主题和标题的搜索查询。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 01/11/2019
ms.author: scottwhi
ms.openlocfilehash: 77f8e41b2271770e49c2e5c4d1591c213eaa257d
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383417"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>使用必应新闻搜索 API 搜索新闻

使用必应图像搜索 API，可以轻松将必应的认知新闻搜索功能集成到应用程序中。

虽然必应新闻搜索 API 主要用于查找并返回相关的新闻文章，但它还提供了一些在网络上进行智能化、集中化新闻检索的功能。

## <a name="suggest-and-use-search-terms"></a>建议并使用搜索词

如果提供供用户输入搜索词的搜索框，请使用[必应自动推荐 API](../../bing-autosuggest/get-suggested-search-terms.md) 来改进体验。 此 API 根据用户键入的部分搜索词返回建议的查询字符串。

用户输入搜索词以后，URL 在设置 [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query) 查询参数之前会对搜索词进行编码。 例如，如果用户输入 sailing dinghies，系统会将 `q` 设置为 `sailing+dinghies` 或 `sailing%20dinghies`  。

## <a name="get-general-news"></a>获取一般新闻

若要从 Web 获取与用户的搜索词相关的普通新闻文章，请发送以下 GET 请求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

如果是首次调用任何必应 API，请勿添加客户端 ID 请求头。 只有在以前调用过必应 API 且必应针对用户和设备组合返回了客户端 ID 的情况下，才包括客户端 ID。

若要从特定的域获取新闻，请使用 [site:](https://msdn.microsoft.com/library/ff795613.aspx) 查询运算符。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

以下展示对上一查询的响应。 作为必应搜索 API 的[使用和显示要求](../useanddisplayrequirements.md)的一部分，你必须按照响应中提供的顺序显示每篇新闻文章。 如果该文章有群集的文章，则应表明存在相关的文章并在用户提出请求时显示它们。

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

[news](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#news) 应答列出必应认为与查询相关的新闻文章。 `totalEstimatedMatches` 字段包含一个估计数，是对可以查看的文章数的估计。 若要了解如何按页浏览这些文章，请参阅[翻页新闻](../paging-news.md)。

该列表中的每篇[新闻](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#newsarticle)包含该文章的名称、说明及其在宿主网站上的 URL。 如果文章包含图像，则该对象包括图像的缩略图。 使用 `name` 和 `url` 可以创建一个超链接，用于将用户转到宿主站点上的新闻。 如果文章包含图像，也请使用 `url` 为图像嵌入可单击的链接。 请务必使用 `provider` 来指定文章的属性。

如果必应可以确定新闻文章的类别，则文章会包含 `category` 字段。

## <a name="get-todays-top-news"></a>获取当天的头条新闻

若要获取当天的头条新闻文章，进行的请求与获取普通新闻相同，但需取消 `q` 的设置。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

获取头条新闻的响应与获取普通新闻的响应几乎相同。 但是，`news` 应答不包含 `totalEstimatedMatches` 字段，因为结果数已设定。 头条新闻文章的数目可能因新闻圈而异。 请务必使用 `provider` 来指定文章的属性。

## <a name="get-news-by-category"></a>按类别获取新闻

若要按类别获取新闻文章（例如头条体育或娱乐文章），请向必应发送以下 GET 请求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

请使用 [category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) 查询参数来指定要获取的文章的类别。 如需可以指定的新闻类别的列表，请参阅 [News Categories by Market](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-categories-by-market)（按市场划分的新闻类别）。

按类别获取新闻的响应与获取普通新闻的响应几乎相同。 区别是，文章全都来自指定的类别。

## <a name="get-headline-news"></a>获取标题新闻

若要请求标题新闻文章以及获取所有新闻类别的文章，请向必应发送以下 GET 请求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

请勿包括 [category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) 查询参数。

获取标题新闻的响应与获取当天的头条新闻的响应相同。 如果文章为标题文章，其 `headline` 字段会设置为 **true**。

默认情况下，响应包含最多 12 篇标题文章。 若要更改需返回的标题文章的数目，请指定 [headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#headlinecount) 查询参数。 就每个新闻类别来说，响应还包括最多四篇非标题文章。

响应将群集文章计为一篇文章。 由于一个群集可能有多篇文章，因此就每个类别来说，响应可能包含 12 篇以上的标题文章，4 篇以上的非标题文章。

## <a name="get-trending-news"></a>获取热门新闻

若要获取社交网络上的热门新闻主题，请向必应发送以下 GET 请求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> 热门主题仅在美国和中国市场提供。

以下 JSON 是对上一请求的响应。 每篇热门新闻文章包含一个相关的图像、突发新闻标志，以及一个链接到该文章的必应搜索结果的 URL。 请使用 `webSearchUrl` 字段中的 URL 将用户带到必应搜索结果页。 也可自行使用查询文本来调用 [Web 搜索 API](../../bing-web-search/search-the-web.md)，以便显示结果。

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>获取相关新闻

如果有其他文章与某篇新闻文章相关，则该新闻文章可能包含 [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle-clusteredarticles) 字段。 下面显示的文章包含群集文章。

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何分页列出必应新闻搜索结果](../paging-news.md)
