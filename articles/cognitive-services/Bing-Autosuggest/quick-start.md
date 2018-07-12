---
title: 自动建议 API 快速入门 | Microsoft Docs
description: 说明如何开始使用必应自动建议 API。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1482E781-7352-4A3F-B1D5-B896381348C4
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a7b54a1fb0b7c76eb72097357a6b51aa02e6e2fd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366460"
---
# <a name="making-your-first-autosuggest-query"></a>进行第一次自动建议查询

在进行第一次调用之前，需要获取认知服务订阅密钥。 若要获取密钥，请参阅[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api)。

若要获得 Web 搜索结果，请向以下终结点发送 GET 请求：

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> V7 预览终结点：
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

请求必须使用 HTTPS 协议。

我们建议所有请求都源自一个服务器。 将密钥作为客户端应用程序的一部分进行分发会为恶意第三方提供更多访问它的机会。 另外，从服务器进行调用会为未来版本的 API 提供单一升级点。

请求必须指定 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) 查询参数，该参数包含用户的部分搜索词。 尽管是可选的，但请求还应该指定 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) 查询参数，该参数确定你希望结果来自的市场。 有关可选查询参数的列表，请参阅[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters)。 所有查询参数值都必须进行 URL 编码。

请求必须指定 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) 标头。 尽管是可选的，但还是建议指定以下标头：

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

客户端 IP 和位置标头对于返回位置感知内容非常重要。

有关所有请求和响应标头列表，请参阅[标头](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers)。

## <a name="the-request"></a>请求

请求应包含所有建议的查询参数和标头。 每次用户在搜索框中键入新字符时，都会调用此 API。 查询字符串的完整性会影响 API 返回的建议查询词的相关性。 查询字符串越完整，建议查询词列表就越相关。 例如，API 可能会为 *s* 返回的建议可能不如它为 *sailing dinghies* 返回的查询相关。 

以下示例显示了一个请求，该请求针对 *sail* 返回建议的查询字符串。

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> V7 预览请求：

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

如果是第一次调用任何必应 API，请勿包括客户端 ID 标头。 只有在以前调用过必应 API 且必应针对用户和设备组合返回了客户端 ID 的情况下，才包括客户端 ID 标头。

以下示例演示对上一请求的响应。 该响应包括 Web 建议组，其中包含搜索查询建议列表。 每个建议都包含 `displayText`、`query` 和 `url` 字段。

`displayText` 字段包含用于填充搜索框下拉列表的建议查询。 必须按给定的顺序显示响应包含的所有建议。  

如果用户从下拉列表中选择查询，你可以使用 `query` 字段中的查询字符串来调用[必应搜索 API](../bing-web-search/search-the-web.md) 并自行显示结果。 或者，可以使用 `url` 字段中的 URL 将用户发送到必应搜索结果页。

```  
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>后续步骤

试用 API。 转到[自动建议 API 测试控制台](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2)。

有关使用响应对象的详细信息，请参阅[获取建议的搜索词](./get-suggested-search-terms.md)。
