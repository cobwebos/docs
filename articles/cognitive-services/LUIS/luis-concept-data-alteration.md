---
title: 数据更改 - LUIS
titleSuffix: Azure Cognitive Services
description: 了解如何在语言理解 (LUIS) 得出预测之前更改数据
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a199821c4db7fd8131ec54700b8c999dfe604a6e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222027"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>在预测之前或预测期间更改话语数据
LUIS 提供在预测之前或预测期间操作陈述的方法。 其中包括[修复拼写](luis-tutorial-bing-spellcheck.md)和修复预生成的[datetimeV2](luis-reference-prebuilt-datetimev2.md)的时区问题。 

## <a name="correct-spelling-errors-in-utterance"></a>更正陈述中的拼写错误

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


LUIS 使用[必应拼写检查 API V7](../Bing-Spell-Check/overview.md) 来更正陈述中的拼写错误。 LUIS 需要与该服务关联的密钥。 创建密钥，然后将密钥添加为[终结点](https://go.microsoft.com/fwlink/?linkid=2092356)的 querystring 参数。 

<!--
You can also correct spelling errors in the **Test** panel by [entering the key](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key is kept as a session variable in the browser for the Test panel. Add the key to the Test panel in each browser session you want spelling corrected. 

Usage of the key in the test panel and at the endpoint count toward the [key usage](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implements Bing Spell Check limits for text length. 

-->

终结点需要两个参数以进行拼写更正：

|Param|值|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[必应拼写检查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 终结点密钥|

[必应拼写检查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 检测到错误时，将一并从终结点返回原始陈述、已更正陈述和预测。

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)
 
```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * * 

### <a name="list-of-allowed-words"></a>允许的字词列表
在 LUIS 中使用的必应拼写检查 API 不支持在拼写检查变更期间忽略的单词的列表。 如果需要允许字词或首字母缩写词的列表，请在将话语发送到 LUIS 进行意向预测之前在客户端应用程序中处理话语。

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>更改预生成 datetimeV2 实体的时区
当 LUIS 应用使用预生成的[datetimeV2](luis-reference-prebuilt-datetimev2.md)实体时，可在预测响应中返回 datetime 值。 请求的时区用于确定要返回的正确日期/时间。 如果请求在到达 LUIS 之前来自机器人或另一个集中式应用程序，则更正 LUIS 使用的时区。 

### <a name="endpoint-querystring-parameter"></a>终结点 querystring 参数
通过使用 [ 参数将用户的时区添加到](https://go.microsoft.com/fwlink/?linkid=2092356)终结点`timezoneOffset`来更正时区。 要更改时间，则 `timezoneOffset` 的值应为正数或负数（以分钟为单位）。  

|Param|值|
|--|--|
|`timezoneOffset`|正数或负数（以分钟为单位）|

### <a name="daylight-savings-example"></a>夏令时示例
如果需要返回的预生成 datetimeV2 来调整夏令时，则对于该`timezoneOffset`终结点[查询应使用值为正数/负数（以分钟为单位）的 ](https://go.microsoft.com/fwlink/?linkid=2092356) querystring 参数。

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 预测终结点请求](#tab/V2)

增加 60 分钟： 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

减去 60 分钟： 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 预测终结点请求](#tab/V3)

增加 60 分钟：

https：//{region}. luis//v2.0 3.0-预览/应用/{appId}/槽/生产/预测？ query = 开启灯光？**timezoneOffset = 60**& 拼写检查 = {boolean} & 必应拼写检查-订阅-密钥 = {string} & log = {boolean}

减去 60 分钟： 

https：//{region}. luis//v2.0 3.0-预览/应用/{appId}/槽/生产/预测？ query = 开启灯光？**timezoneOffset =-60**& 拼写检查 = {boolean} & 必应拼写检查--key = {string} & log = {boolean}

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C# 代码确定正确的 timezoneOffset 值
下面的 C# 代码使用 [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) 类的 [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) 方法基于系统时间来确定正确的 `timezoneOffset`：

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过本教程更正拼写错误](luis-tutorial-bing-spellcheck.md)
