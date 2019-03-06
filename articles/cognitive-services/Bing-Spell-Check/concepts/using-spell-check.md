---
title: 使用必应拼写检查 API
titlesuffix: Azure Cognitive Services
description: 了解必应拼写检查模式、设置和与 API 相关的其他信息。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 9544337ef1322e52cbdf123bb48d283485a8c7dd
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890775"
---
# <a name="using-the-bing-spell-check-api"></a>使用必应拼写检查 API

通过本文了解如何使用必应拼写检查 API 执行上下文语法和拼写检查。 虽然大多数拼写检查器都依赖于基于字典的规则集，但必应拼写检查器利用机器学习和统计性机器翻译来提供准确的上下文更正。 

## <a name="spell-check-modes"></a>拼写检查模式

此 API 支持两种检查模式：`Proof` 和 `Spell`。  请尝试[此处](https://azure.microsoft.com/services/cognitive-services/spell-check/)的示例。

### <a name="proof---for-documents"></a>Proof - 适用于文档 

默认模式为`Proof`。 `Proof` 拼写模式提供最广泛的检查，可以添加大写、基本标点符号和其他功能，帮助用户创建文档。 但是，它仅在 en-US（英语-美国）、es-ES（西班牙语）、pt-BR（葡萄牙语）市场推出（注意：西班牙语和葡萄牙语仅提供 Beta 版本）。 对于所有其他市场，请将 mode 查询参数设置为 Spell。 

> [!NOTE]
> 如果查询文本长度超出 4096，系统会将其截断，仅保留 4096 个字符，然后对其进行处理。 

### <a name="spell----for-web-searchesqueries"></a>Spell - 适用于 Web 搜索/查询

`Spell` 性能更强，可以返回更好的搜索结果。 `Spell` 模式可以找出大多数拼写错误，但找不出 `Proof` 捕获的某些语法错误，例如大写和重复单词。

> [!NOTE]
> * 支持的最大查询长度如下。 如果查询超出最大长度，则查询及其结果不会更改。
>    * 130 个字符的限制适用于以下语言代码：en、de、es、fr、pl、pt、sv、ru、nl、nb、tr-tr、it、zh、ko。 
>    * 65 个字符的限制适用于所有其他语言代码。
> * “拼写”模式不支持在查询中使用方括号字符（`[` 和 `]`），否则可能导致结果不一致。 建议在使用“拼写”模式时从查询中删除它们。

## <a name="market-setting"></a>市场设置

应在请求中使用 `mkt` 查询参数指定[市场代码](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#market-codes)。 否则，API 将使用基于请求 IP 地址的默认市场。


## <a name="http-post-and-get-support"></a>HTTP POST 和 GET 支持

此 API 支持 HTTP POST 或 HTTP GET。 使用哪一个取决于计划进行校对的文本的长度。 如果字符串始终不到 1,500 个字符，则使用 GET。 但如果需要支持长达 10,000 个字符的字符串，则使用 POST。 文本字符串可能包含任何有效的 UTF-8 字符。

以下示例显示的 POST 请求用于检查文本字符串的拼写和语法。 此示例包含针对完整性的 [mode](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#mode) 查询参数（可能已省略，因为 `mode` 默认为 Proof）。 [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) 查询参数包含要检查的字符串。
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

如果使用 HTTP GET，则应在 URL 的查询字符串中包括 `text` 查询参数
  
下面的示例展示了对上一请求的响应。 此响应包含 [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#spellcheck) 对象。 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
[flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#flaggedtokens) 字段列出了 API 在[文本](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text)字符串中发现的拼写和语法错误。 `token` 字段包含要替换的词。 可以使用 `offset` 字段中基于零的偏移来查找 `text` 字符串中的令牌。 然后，使用该位置的词来替换 `suggestion` 字段中的词。 

如果 `type` 字段为 RepeatedToken，仍需将令牌替换为 `suggestion`，但可能还需删除尾随空格。

## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>后续步骤

- [什么是必应拼写检查 API？](../overview.md)
- [必应拼写检查 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
