---
title: 使用必应视频搜索 API 在 Web 上搜索热门视频
titlesuffix: Azure Cognitive Services
description: 了解如何使用必应视频搜索 API 在 Web 上搜索热门视频。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 486cf2e3bcf851f23011bb2fb8d91691d6190698
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61431910"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>使用必应视频搜索 API 获取热门视频 

借助必应视频搜索 API，可以跨 Web 查找当前各种类别的热门视频。 

## <a name="get-request"></a>GET 请求

若要从必应视频搜索 API 获取当前的热门视频，请发送以下 GET 请求：  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>市场支持

以下市场支持热门视频。  
 
-   en-AU（英语，澳大利亚）  
-   en-CA（英语，加拿大）  
-   en-GB（英语，英国）  
-   en-ID（英语，印度尼西亚）  
-   en-IE（英语，爱尔兰）  
-   en-IN（英语，印度）  
-   en-NZ（英语，新西兰）  
-   en-PH（英语，菲律宾）  
-   en-SG（英语，新加坡）  
-   en-US（英语，美国）  
-   en-WW（英语，全球汇总代码）  
-   en-ZA（英语，南非）  
-   zh-CN（中文，中国）

## <a name="example-json-response"></a>示例 JSON 响应  

以下示例展示了一个包含热门视频的 API 响应，这些视频按类别和子类别列出。 该响应还包含横幅视频，这些是最受欢迎的热门视频，可以来自一个或多个类别。  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取视频见解](video-insights.md)