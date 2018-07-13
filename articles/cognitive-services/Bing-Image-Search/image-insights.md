---
title: 获取图像见解 | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: 介绍如何使用必应图像搜索 API 获取图像的详细信息。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: f651d9f773f475e633aed698e134aa6a7c07393b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366464"
---
# <a name="get-insights-about-an-image"></a>获取图像的见解

> [!IMPORTANT]
> 应该使用[视觉搜索](../bing-visual-search/overview.md)而不是 /images/details 终结点来获取图像见解，因为前者提供的见解更全面。


每个图像包含一个见解令牌，可以用来获取图像的信息。 例如，可以获取一组相关的图像、包含图像的网页、或者商家（可以从其购买图像中所示产品）的列表。  
  
若要获取图像的见解，请在响应中捕获图像的 [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) 令牌。 

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

接下来，调用“图像详细信息”终结点，并将 [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) 查询参数设置为 `imageInsightsToken` 中的令牌。  

若要指定需获取的见解，请设置 `modules` 查询参数。 若要获取所有见解，请将 `modules` 设置为 `All`。 若只获取标题和集合见解，请将 `modules` 设置为 `Caption%2CCollection`。 如需可能的见解的完整列表，请参阅 [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested)。 并不为所有图像提供所有见解。 响应包括请求的所有见解（如果提供）。

以下示例请求提供给上一图像的所有见解。

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
``` 

## <a name="getting-insights-of-a-known-image"></a>获取已知图像的见解

如果有要获取其见解的图像的 URL，请使用 [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) 查询参数而不是 [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) 参数来指定图像。 或者，如果有图像文件，则可在 POST 请求的正文中发送图像的二进制文件。 如果使用 POST 请求，则必须将 `Content-Type` 头设置为 `multipart/data-form`。 不管哪个选项，图像的大小都不得超出 1 MB。  
  
如果有图像的 URL，则可参照以下示例来请求图像的见解。

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
``` 
  
## <a name="getting-all-image-insights"></a>获取所有图像见解  

若要请求某个图像的所有见解，请将 [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) 查询参数设置为 `All`。 若要获取相关的搜索，该请求必须包含用户的查询字符串。 以下示例演示如何使用 [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) 来指定图像。  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

顶级对象是 [ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) 对象而不是 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 对象。  
  
```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>识别图像中的实体  

实体识别功能可识别图像中的实体（目前只识别人）。 若要标识某个图像中的实体，请将 [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) 查询参数设置为 `RecognizedEntities`。  

> [!NOTE]
> 不得使用任何其他模块来指定此模块。 如果使用其他模块来指定此模块，则响应不包含识别的实体。  
  
以下示例演示如何使用 [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) 参数来指定图像。 记住对查询参数进行 URL 编码。  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  
  
以下示例演示对上一请求的响应。 由于图像包含两个人，响应会标识每个人的区域。 在此示例中，系统认为这两人属于 CelebrityAnnotations 和 CelebRecognitionAnnotations 组。 必应列出每个组中的人时，其依据是该人与原始图像中的人匹配的可能性。 列表按置信度以降序方式排列。 就匹配的正确性来说，CelebRecognitionAnnotations 组提供的置信度最高。  
  
```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```
  
`region` 字段标识必应识别实体时所在的图像区域。 就人来说，该区域代表人的面孔。  
  
矩形的值是相对于原始图像的宽和高的，其范围为 0.0 到 1.0。 例如，如果图像为 300x200，该区域左上角所在的点为 (10, 20)，右下角所在的点为 (290, 150)，则规范化的矩形的指标如下：  
  
-   左：10 / 300 = 0.03333...  
-   上：20 / 200 = 0.1  
-   右：290 / 300 = 0.9667...  
-   下：150 / 200 = 0.75  
  
可以在后续的见解调用中使用必应返回的此区域。 例如，可以使用此区域来获取在视觉上与识别的实体类似的图像。 有关详细信息，请参阅[裁剪图像以便将其用于“视觉类似”模块和“实体识别”模块](#croppingimages)。 下面说明了如何在区域字段和将要用于裁剪图像的查询字符串之间进行映射。  
  
-   “左”映射到 [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
-   “上”映射到 [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
-   “右”映射到 [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
-   “下”映射到 [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>查找视觉类似的图像  

若要查找视觉上类似于原始图像的图像，请将 [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) 查询参数设置为 SimilarImages。  
  
以下请求演示如何获取视觉类似图像。 此请求使用 [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) 查询参数来标识原始图像。 若要提高相关性，则应包括用户的查询字符串。  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

  
以下示例演示对上一请求的响应。  
  
```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```
  
## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>裁剪图像以便将其用于“视觉类似”模块和“实体识别”模块  

若要指定图像区域，以便必应将其用来确定图像是否在视觉上类似或执行实体识别操作，请使用 [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)、[cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)、[cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab) 和 [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car) 查询参数。 默认情况下，必应使用整个图像。  
  
这些参数指定区域的左上角和右下角，供必应进行比较。 将这些值指定为原始图像的宽和高的分数。 分数值最小为 (0.0, 0.0)，表示左上角；最大为 (1.0, 1.0)，表示右下角。 例如，若要指定一个左上角的点，该点到上部边缘和左侧边缘的距离均为整个高和宽的四分之一，则请将 `cal` 和 `cat` 均设置为 0.25。  
  
下面的一系列调用显示了指定裁剪区域的效果。 第一个调用不包括裁剪，必应识别出两个人并排站在图像中间。  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

响应显示两个识别的实体。  
  
```json 
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  
  
第二个调用沿着中间对图像进行了垂直裁剪，必应识别出一个人站在图像右侧。  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

响应显示一个识别的实体。  
  
```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  
  
## <a name="finding-visually-similar-products"></a>查找视觉类似的产品  

若要查找特定的图像，其包含的产品在视觉上类似于原始图像中发现的产品，请将 [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) 查询参数设置为 SimilarProducts。  
  
以下请求演示如何获取视觉类似产品的图像。 此请求使用 [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) 查询参数来标识在前面的一个请求中返回的原始图像。 若要提高相关性，则应包括用户的查询字符串。  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```
  
以下示例演示对上一请求的响应。 响应包含一个类似产品的图像，并指出多少商家在线提供此产品、是否有产品评级，以及找到的最低价格（参见 `aggregateOffer` 字段）。  
  
```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```
  
若要获取在线提供此产品的商家的列表（参见 [offerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer-offercount) 字段），请再次调用此 API，将 `modules` 设置为 ShoppingSources。 然后，将 `insightsToken` 查询参数设置为在产品摘要图像中发现的令牌。  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

以下示例是对上一请求的响应。  
  
```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```
