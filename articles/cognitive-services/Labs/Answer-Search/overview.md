---
title: 项目答案搜索概述 - Microsoft 认知服务 | Microsoft Docs
description: 项目答案搜索简介。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: d87cf1390970d2c815b94bcaee7e07c19bc03cce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366306"
---
# <a name="what-is-project-answer-search"></a>什么是项目答案搜索？
项目答案搜索 API 使用 Bing v7 终结点获取疑问式询问的答案。 “地球的周长是多少？”此类问题 返回带有事实信息的答案。  对人员、地点或事物的查询则返回与查询识别的实体相关的信息。 这些方案在对话机器人、消息应用、读取器等应用程序中十分有用。  

查询返回取决于查询方案的响应：始终返回网页，同时如果相关，则返回[事实](fact-queries.md)和/或[实体](entity-queries.md)。

## <a name="endpoint"></a>终结点
要获取问题答案或人员、地点或事物相关信息，请向答案搜索 API 终结点发送请求。 使用标头和 URL 参数获取各类规范。  借助有效令牌包括“Ocp-Apim-Subscription-Key”标头。  市场参数是必需的。 当前仅支持 `en-us` 市场。

以下查询可获取此问题的答案：“地球的周长是多少？”

得：
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

````

需要使用 URL 参数 `q=` 来指定搜索对象。

## <a name="response-object"></a>响应对象

响应包括 HTTP 标头、网页、事实和/或实体。

````
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "http://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

````

## <a name="terms-of-use"></a>使用条款
项目答案搜索和项目视频趋势受[必应搜索使用和显示要求](use-display-requirements.md)约束。

你或代表你的第三方不得出于测试、开发、定型、分发或提供任何非 Microsoft 服务或功能目的使用、保留、存储、缓存、共享或分发来自 URL 预览 API 的任何数据。 

## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>数据属性  

项目答案搜索响应包含第三方拥有的信息。 有责任确保使用适当，例如通过遵守用户体验可能依据的任何 Creative Commons 许可证要求。  
  
如果答案或结果包括 `contractualRules`、`attributions` 或 `provider` 字段，则必须对数据进行归属。 如果答案不包含这些字段中的任意一个，则无需进行归属。 如果答案包括 `contractualRules` 字段以及 `attributions` 和/或 `provider` 字段，则必须使用合同规则对数据进行归属。  
  
以下示例显示了包括 MediaAttribution 合同规则的实体和包括 `provider` 字段的图像。 MediaAttribution 规则将图像识别为规则的目标，因此将忽略图像的 `provider` 字段，改用 MediaAttribution 规则提供属性。  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
如果合同规则包括 `targetPropertyName` 字段，则该规则仅适用于目标字段。 否则，该规则适用于包含 `contractualRules` 字段的父对象。  
  
  
在以下示例中，`LinkAttribution` 规则包括 `targetPropertyName` 字段，因此该规则适用于 `description` 字段。 对于适用于特定字段的规则，必须包括紧接目标数据的一行，其中目标数据包含指向提供程序网站的超链接。 例如，要对描述进行归属，请包括紧接描述文本的一行，其中描述文本包含指向提供程序网站上数据的超链接，在这种情况下创建指向 en.wikipedia.org 的链接。  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>许可证属性  

如果合同规则列表包括 [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) 规则，则必须在紧接许可证适用内容的一行上显示通知。 `LicenseAttribution` 规则使用 `targetPropertyName` 字段识别许可证适用的属性。  
  
以下内容显示了包括 `LicenseAttribution` 规则的示例。  
  
![许可证属性](./media/licenseattribution.png)  
  
你显示的许可证通知必须包括指向包含许可证相关信息的网站的超链接。 通常会将许可证名称作为超链接。 例如，如果通知是“Text under CC-BY-SA”许可证，其中 CC-BY-SA 是许可证名称，则使 CC-BY-SA 成为超链接。  
  
### <a name="link-and-text-attribution"></a>链接和文本属性  

[LinkAttribution](reference.md#linkattribution) 和 [TextAttribution](reference.md#textattribution) 规则通常用于识别数据提供程序。 `targetPropertyName` 字段识别该规则适用的字段。  
  
要归属提供程序，则包括紧跟属性适用内容的一行（例如，目标字段）。 应明确标记该行，以指示提供程序是数据的来源。 例如，“数据来自：en.wikipedia.org”。 对于 `LinkAttribution` 规则，必须创建指向提供程序网站的超链接。  
  
以下内容显示了包括 `LinkAttribution` 和 `TextAttribution` 规则的示例。  
  
![链接文本属性](./media/linktextattribution.png)  

### <a name="media-attribution"></a>媒体属性  

如果实体包括图像并且将其显示，则必须提供指向提供程序网站的点击链接。 如果实体包括 [MediaAttribution](reference.md#mediaattribution) 规则，则使用规则的 URL 创建点击链接。 否则，请使用包括在图像的 `provider` 字段中的 URL 创建点击链接。  
  
以下内容显示了包括图像的 `provider` 字段和合同规则的示例。 因为该示例包括合同规则，则将忽略图像的 `provider` 字段并应用 `MediaAttribution` 规则。  
  
![媒体属性](./media/mediaattribution.png)  

## <a name="next-steps"></a>后续步骤
- [C# 快速入门](c-sharp-quickstart.md)
- [Java 快速入门](java-quickstart.md)
- [Node 快速入门](node-quickstart.md)
- [Python 快速入门](python-quickstart.md)
