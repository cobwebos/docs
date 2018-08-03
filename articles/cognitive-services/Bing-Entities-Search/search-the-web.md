---
title: 什么是必应实体搜索？ | Microsoft Docs
description: 了解如何使用必应实体搜索 API 搜索 Web 中的实体和场所。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0B54E747-61BF-42AA-8788-E25D63F625FC
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2016
ms.author: scottwhi
ms.openlocfilehash: 275430bc6ee8f935978243e61f68713974648189
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008104"
---
# <a name="what-is-bing-entity-search"></a>什么是必应实体搜索？

必应实体搜索 API 向必应发送搜索查询，然后获得包含实体和场所的结果。 场所结果包括餐馆、酒店或其他本地商业场所。 如果查询指定了本地商业场所的名称或询问某类商业场所（例如，“我附近的餐馆”），则必应会返回场所。 如果查询指定知名的人物、场所（旅游景点、州、县等）或事物，则必应会返回实体。

## <a name="suggesting--using-search-terms"></a>建议和使用搜索词

如果提供供用户输入搜索词的搜索框，请使用[必应自动建议 API](../bing-autosuggest/get-suggested-search-terms.md) 来改进体验。 此 API 根据用户键入的部分搜索词返回建议的查询字符串。

用户输入搜索词以后，URL 在设置 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) 查询参数之前会对搜索词进行编码。 例如，如果用户输入 *Marcus Appel*，则将 `q` 设置为 *Marcus+Appel* 或 *Marcus%20Appel*。

如果搜索词包含拼写错误，则搜索响应会包含 [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) 对象。 该对象显示原始拼写以及必应用于搜索的已更正拼写。

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="requesting-entities"></a>请求实体

如需示例请求，请参阅[发出第一个请求](./quick-start.md)。

## <a name="the-response"></a>响应

响应包含 [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse) 对象。 如果必应找到相关的实体或场所，则此对象会包含 `entities` 和/或 `places` 字段。 否则，响应对象不包含任一字段。
> [!NOTE]
> 实体响应支持多个市场，但 Places 响应仅支持美国业务位置。 

`entities` 字段是一个 [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entityanswer) 对象，该对象包含 [Entity](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) 对象的列表（参见 `value` 字段）。 此列表可能包含单个主实体和/或多个消除歧义实体。 

主实体是必应认为的符合请求的唯一实体（对于哪个实体符合请求不存在争议）。 如果多个实体可能符合请求，则列表包含多个消除歧义实体。 例如，如果请求使用某个电影系列的宽泛的名称，则列表可能包含多个消除歧义实体。 但是，如果请求指定了来自该系列的一个具体的名称，则列表可能包含单个主实体。

实体包括知名人士（例如歌唱家、演员、运动员、模特等）、场所和地标（例如瑞尼尔山或林肯纪念堂）、事物（例如香蕉、黄金贵宾犬、书或电影名称）。 [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) 字段包含的提示用于标识实体的类型。 例如，它是人物、电影、动物还是景点。 如需可能的类型的列表，请参阅[实体类型](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

下面显示的一个响应包含一个主要的消除歧义实体。

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
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
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

该实体包含 `name`、`description` 和 `image` 字段。 在用户体验中显示这些字段时，必须确定其归属。 `contractualRules` 字段包含必须应用的属性的列表。 协定规则标识属性所应用到的字段。 若要了解如何应用属性，请参阅[属性](#data-attribution)。

```json
"contractualRules": [{
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
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

显示实体信息（名称、说明和图像）时，还必须使用 `webSearchUrl` 字段中的 URL 链接到包含实体的必应搜索结果页。


`places` 字段是一个 [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer) 对象，该对象包含 [Place](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) 对象的列表（参见 `value` 字段）。 此列表包含一个或多个符合请求的本地实体。

场所包括餐馆、酒店或本地商业场所。 [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) 字段包含的提示用于标识本地实体的类型。 此列表包含一系列提示，例如场所、本地商业场所、餐馆。 数组中的每个后续提示都会缩窄实体的类型范围。 如需可能的类型的列表，请参阅[实体类型](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> 实体响应支持多个市场，但 Places 响应仅支持美国业务位置。 

本地感知型实体查询（例如“我附近的餐馆”）需要用户的位置才能提供准确结果。 请求应始终使用 X-Search-Location 和 X-MSEdge-ClientIP 标头来指定用户的位置。 如果必应认为查询可以利用用户的位置，它会将 [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) 的 `askUserForLocation` 字段设置为 **true**。 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

场所结果包括场所的名称、地址、电话号码以及实体网站的 URL。 显示实体信息时，还必须使用 `webSearchUrl` 字段中的 URL 链接到包含实体的必应搜索结果页。

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> 你或代表你的第三方不得出于测试、开发、训练、分发或提供任何非 Microsoft 服务或功能目的使用、保留、存储、缓存、共享或分发来自实体 API 的任何数据。  

## <a name="data-attribution"></a>数据属性

必应实体 API 响应包含第三方拥有的信息。 你有责任确保使用适当，例如通过遵守用户体验可能依据的任何 Creative Commons 许可证。

如果答案或结果包含 `contractualRules`、`attributions` 或 `provider` 字段，则必须对数据归类。 如果答案不包含这些字段中的任意一个，则无需进行归属。 如果答案包括 `contractualRules` 字段以及 `attributions` 和/或 `provider` 字段，则必须使用合同规则对数据进行归属。

以下示例显示了包括 MediaAttribution 合同规则的实体和包括 `provider` 字段的图像。 MediaAttribution 规则将图像识别为规则的目标，因此将忽略图像的 `provider` 字段，改用 MediaAttribution 规则提供属性。  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

如果合同规则包括 `targetPropertyName` 字段，则该规则仅适用于目标字段。 否则，该规则适用于包含 `contractualRules` 字段的父对象。

在以下示例中，`LinkAttribution` 规则包含 `targetPropertyName` 字段，因此该规则适用于 `description` 字段。 对于适用于特定字段的规则，必须紧随提供商网站的超链接所在的目标数据包含一行。 例如，若要对说明归类，请紧随提供商网站上的数据的超链接所在的说明文本包括一行，在此示例中，就是创建 contoso.com 的链接。

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>许可证属性

如果协定规则的列表包含 [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) 规则，则必须在紧随许可证应用到的内容的行中显示声明。 `LicenseAttribution` 规则使用 `targetPropertyName` 字段识别许可证适用的属性。

以下内容显示了包括 `LicenseAttribution` 规则的示例。

![许可证属性](./media/cognitive-services-bing-entities-api/licenseattribution.png)

你显示的许可证通知必须包括指向包含许可证相关信息的网站的超链接。 通常会将许可证名称作为超链接。 例如，如果声明是“文本需 CC-BY-SA 许可证”，且 CC-BY-SA 是许可证名称，则让 CC-BY-SA 成为超链接。

### <a name="link-and-text-attribution"></a>链接和文本属性

[LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) 和 [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) 规则通常用于识别数据提供程序。 `targetPropertyName` 字段识别该规则适用的字段。

要归属提供程序，则包括紧跟属性适用内容的一行（例如，目标字段）。 该行应明确标记，指示提供商是数据的来源。 例如，“数据来自: contoso.com”。 对于 `LinkAttribution` 规则，必须创建指向提供商网址的超链接。

以下内容显示了包括 `LinkAttribution` 和 `TextAttribution` 规则的示例。

![链接文本属性](./media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>媒体属性

如果实体包括图像而该图像会显示，则必须提供指向提供商网站的点击链接。 如果实体包括 [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution) 规则，则使用规则的 URL 创建点击链接。 否则，请使用包括在图像的 `provider` 字段中的 URL 创建点击链接。

下面显示的一个示例包含图像的 `provider` 字段和协定规则。 由于此示例包含协定规则，请忽略图像的 `provider` 字段并应用 `MediaAttribution` 规则。

![媒体属性](./media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>搜索或类搜索体验

就像使用必应 Web 搜索 API 一样，只有在进行直接用户查询或搜索的情况下才能使用必应实体搜索 API。如果在应用或体验中进行的操作在逻辑上可以解释为用户的搜索请求，则也可这样做。 为了便于说明这一点，下面提供了一些可接受的搜索或类搜索体验的示例。

- 用户直接将查询输入应用的搜索框中
- 用户选择具体的文本或图像，然后请求“更多信息”或“其他信息”
- 用户向搜索机器人询问特定的主题
- 在视觉搜索类型方案中，用户着眼于特定的对象或实体

如果不确定自己的体验是否可以被视为类搜索体验，建议向 Microsoft 咨询。

## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>后续步骤

若要快速了解如何使用第一个请求，请参阅[发出第一个请求](./quick-start.md)。

自行熟悉[必应实体搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) 参考文档。 此参考文档包含可以用来请求搜索结果的标头和查询参数， 此外还包含响应对象的定义。 

若要改进搜索框用户体验，请参阅[必应自动建议 API](../bing-autosuggest/get-suggested-search-terms.md)。 当用户输入其查询词时，你可以调用此 API，以便获取他人所使用的相关查询词。

确保阅读[必应使用和显示要求](./use-display-requirements.md)，以免违反关于搜索结果使用要求的任何规则。