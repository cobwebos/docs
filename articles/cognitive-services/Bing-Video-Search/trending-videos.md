---
title: 搜索 Web 上的热门视频 | Microsoft Docs
description: 演示如何使用必应视频搜索 API 在 Web 上搜索热门视频。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 897A28A3-0980-484E-814F-FFE1D5C885E6
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 8db7fcf77042631260b4b165bd3d44053827f3ce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365406"
---
# <a name="get-trending-videos"></a>获取热门视频  

要获取今天的热门视频，请发送以下 GET 请求：  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

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

  
以下示例显示包含热门视频的响应。  

```  
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
响应按类别和子类别包含视频列表。 例如，如果类别列表包含“音乐视频”类别且其子类别之一为“热门”，则可以在用户体验中创建“热门音乐视频”类别。 然后，你可以使用 `thumbnailUrl`、`displayText` 和 `webSearchUrl` 字段在每个类别下创建可单击的磁贴（例如，热门音乐视频）。 当用户单击磁贴时，系统会转到播放视频的必应视频浏览器中。

该响应还包含横幅视频，这是最受欢迎的热门视频。 横幅视频可以来自一个或多个类别。  
  
