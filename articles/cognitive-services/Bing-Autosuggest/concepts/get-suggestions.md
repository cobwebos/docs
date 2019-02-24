---
title: 使用必应自动建议 API 来建议搜索词
titlesuffix: Azure Cognitive Services
description: 了解如何使用必应自动建议 API。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/06/2019
ms.author: aahi
ms.openlocfilehash: 8460f282a4372b4297b373bad669f56f2932e323
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271605"
---
# <a name="suggesting-query-terms"></a>建议查询词

通常，每次用户在应用程序的搜索框中键入新字符时，你都会调用必应自动建议 API。 查询字符串的完整性会影响 API 返回的建议查询词的相关性。 查询字符串越完整，建议的查询词列表就越相关。 例如，API 可能会为 `s` 返回的建议可能不如它为 `sailing dinghies` 返回的查询相关。

## <a name="example-request"></a>示例请求

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

## <a name="using-suggested-query-terms"></a>使用建议查询词

每个建议都包括 `displayText`、`query` 和 `url` 字段。 `displayText` 字段包含用于填充搜索框下拉列表的建议查询。 必须按给定的顺序显示响应包含的所有建议。

下面的示例显示了一个下拉搜索框，其中包含 Bing 自动建议 API 提供的建议查询词。

![自动建议下拉搜索框列表](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

如果用户从下拉列表中选择了一个建议的查询，则你将使用 `query` 字段中的查询词来调用[必应 Web 搜索 API](../../bing-web-search/search-the-web.md) 并自行显示结果。 或者，可以使用 `url` 字段中的 URL 将用户发送到必应搜索结果页。

## <a name="next-steps"></a>后续步骤

* [什么是必应自动建议 API？](../get-suggested-search-terms.md)