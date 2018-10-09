---
title: 什么是必应自动建议？ | Microsoft Docs
description: 了解如何使用必应自动建议 API。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 6F4AFEDA-71A7-48C1-B3E2-D0D430428CDC
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: 76e509289f495e09c367af926fd26e0581b6e7c6
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091100"
---
# <a name="what-is-bing-autosuggest"></a>什么是必应自动建议？

如果向任何必应搜索 API 发送查询，可以使用必应自动建议 API 来改进你的搜索框体验。 必应自动推荐 API 根据用户在搜索框中输入的部分查询字符串返回建议查询的列表。 可以在搜素框的下拉列表中显示建议。 建议的词语是根据其他用户已搜索的建议查询以及用户意向提供的。

通常，每当用户在搜索框中键入新字符时，都会调用此 API。 查询字符串的完整性会影响 API 返回的建议查询词的相关性。 查询字符串越完整，建议的查询词列表就越相关。 例如，API 可能会为 *s* 返回的建议可能不如它为 *sailing dinghies* 返回的查询相关。

## <a name="getting-suggested-search-terms"></a>获取建议的搜索词

以下示例显示了一个请求，该请求针对 *sail* 返回建议的查询字符串。 在设置 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query) 查询参数时，请记得对用户的部分查询词进行 URL 编码。 例如，如果用户输入了 *sailing les*，请将 `q` 设置为 `sailing+les` 或 `sailing%20les`。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

以下响应列出了包含建议查询词的 [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction) 对象。

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

每个建议都包括 `displayText`、`query` 和 `url` 字段。 `displayText` 字段包含用于填充搜索框下拉列表的建议查询。 必须按给定的顺序显示响应包含的所有建议。

下面显示了包含建议查询词的下拉搜索框的示例。

![自动建议下拉搜索框列表](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

如果用户从下拉列表中选择了一个建议的查询，则你将使用 `query` 字段中的查询词来调用[必应 Web 搜索 API](../bing-web-search/search-the-web.md) 并自行显示结果。 或者，可以使用 `url` 字段中的 URL 将用户发送到必应搜索结果页。

## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>后续步骤

若要快速了解如何使用第一个请求，请参阅[创建第一个查询](quickstarts/csharp.md)。

使自己熟悉[必应自动建议 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) 参考文档。 此参考文档包含可以用来请求建议查询词的终结点、标头和查询参数的列表，以及响应对象的定义。

了解如何使用[必应 Web 搜索 API](../bing-web-search/search-the-web.md) 搜索网页。

确保阅读[必应使用和显示要求](./useanddisplayrequirements.md)，以免违反关于搜索结果使用要求的任何规则。