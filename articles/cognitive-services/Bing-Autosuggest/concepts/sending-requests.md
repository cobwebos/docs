---
title: 将请求发送到必应自动建议 API
titleSuffix: Azure Cognitive Services
description: 了解如何将请求发送到必应自动建议 API。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: dbc8e2ccbd0a9d8c395a3b4e8a95e4e51f152792
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882421"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>将请求发送到必应自动建议 API。

如果应用程序向任何必应搜索 API 发送查询，可以使用必应自动建议 API 来改进用户的搜索体验。 必应自动建议 API 根据搜索框中的部分查询字符串返回建议查询的列表。 在应用程序的搜索框中输入字符时，可以在下拉列表中显示建议。 通过本文详细了解如何向该 API 发送请求。 

## <a name="bing-autosuggest-api-endpoint"></a>必应自动建议 API 终结点

必应自动推荐 API 包含一个终结点，这将从部分搜索词返回建议的查询列表。

若要使用必应 API 获取建议的查询，请向以下终结点发送 `GET` 请求。 使用标头和 URL 参数来定义更多规范。

**终结点：** 将搜索建议作为 JSON 结果返回，该结果与 `?q=""` 定义的用户输入相关。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

有关标头、参数、市场代码、响应对象、错误等的详细信息，请参阅[必应自动建议 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) 参考。

必应 API 支持根据其类型返回结果的搜索操作。 所有搜索终结点均将结果作为 JSON 响应对象返回。
所有终结点支持后列查询：按经度、纬度和搜索半径返回特定语言和/或位置的查询。

有关每个终结点支持的参数的完整信息，请参阅每个类型的参考页面。
有关使用自动建议 API 的基本请求的示例，请参阅[自动建议快速入门](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest)。

## <a name="bing-autosuggest-api-requests"></a>必应自动建议 API 请求

> [!NOTE]
> * 发送到必应自动建议 API 的请求必须使用 HTTPS 协议。

建议所有请求都源自服务器。 如果将密钥作为客户端应用程序的一部分进行分发，会让恶意的第三方有更多机会进行访问。 另外，从服务器执行调用还会提供未来更新的单一升级点。

请求必须指定 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) 查询参数，该参数包含用户的部分搜索词。 尽管是可选的，但请求还应该指定 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) 查询参数，该参数确定你希望结果来自的市场。 有关可选查询参数的列表，请参阅[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters)。 所有查询参数值都必须进行 URL 编码。

请求必须指定 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) 标头。 尽管可视需要添加，但仍建议还指定以下请求头：

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

客户端 IP 和位置请求头对返回位置感知内容非常重要。

有关所有请求头和响应头的列表，请参阅[头](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers)。

> [!NOTE]
> 从 JavaScript 调用必应自动建议 API 时，浏览器内置的安全功能可能会阻止你访问这些标头的值。

若要解决此问题，可以通过 CORS 代理发出必应自动建议 API 请求。 来自此类代理的响应有一个 `Access-Control-Expose-Headers` 标头，此标头将响应标头列入允许列表，并将它们提供给 JavaScript。

可以轻松安装 CORS 代理，使我们的[教程应用](../tutorials/autosuggest.md)可以访问可选的客户端标头。 首先，如果尚未安装 Node.js，请先[安装](https://nodejs.org/en/download/)。 然后在命令提示符处键入以下命令。

    npm install -g cors-proxy-server

接下来，在 HTML 文件中将必应自动建议 API 终结点更改为：

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

最后，使用以下命令启动 CORS 代理：

    cors-proxy-server

使用教程应用期间，不要关闭命令窗口；关闭窗口会导致代理停止运行。 在搜索结果下的可展开 HTTP 响应头部分中，现在可以看到 `X-MSEdge-ClientID` 响应头（以及其他响应头），并验证此响应头是否对所有请求都相同。

请求应包含所有建议的查询参数和标头。 

以下示例显示了一个请求，该请求针对 *sail* 返回建议的查询字符串。

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

如果是首次调用任何必应 API，请勿添加客户端 ID 请求头。 只有在以前调用过必应 API 且必应针对用户和设备组合返回了客户端 ID 的情况下，才包括客户端 ID 标头。

以下 Web 建议组是对上面请求的响应。 该组包含搜索查询建议的列表，每条建议包括 `displayText`、`query` 和 `url` 字段。

`displayText` 字段包含用于填充搜索框下拉列表的建议查询。 必须按给定的顺序显示响应包含的所有建议。  

如果用户从下拉列表中选择查询，你可以使用它来调用某个[必应搜索 API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) 并自行显示结果，也可以使用返回的 `url` 字段将用户发送到必应结果页。

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
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

- [什么是必应自动建议？](../get-suggested-search-terms.md)
- [必应自动推荐 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [从必应自动建议 API 获取建议的搜索词](get-suggestions.md)
