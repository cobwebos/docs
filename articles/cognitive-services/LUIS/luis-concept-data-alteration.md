---
title: 数据更改 - LUIS
description: 了解如何在语言理解 (LUIS) 得出预测之前更改数据
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 3a88739caa9b35679f10b0cb63a804e9464c871c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872251"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>在预测之前或预测期间更改话语数据
LUIS 提供在预测之前或预测期间操作陈述的方法。 其中包括[修复拼写](luis-tutorial-bing-spellcheck.md)和修复预生成的[datetimeV2](luis-reference-prebuilt-datetimev2.md)的时区问题。

## <a name="correct-spelling-errors-in-utterance"></a>更正陈述中的拼写错误


### <a name="v3-runtime"></a>V3 运行时

在将查询文本发送到 LUIS 之前，预处理文本的拼写更正。 使用带有正确拼写的示例最谈话，以确保获得正确的预测。

将文本发送到 LUIS 之前，请使用[必应拼写检查](../bing-spell-check/overview.md)更正文本。

### <a name="prior-to-v3-runtime"></a>在 V3 运行时之前

LUIS 使用[必应拼写检查 API V7](../Bing-Spell-Check/overview.md) 来更正陈述中的拼写错误。 LUIS 需要与该服务关联的密钥。 创建密钥，然后将密钥添加为[终结点](https://go.microsoft.com/fwlink/?linkid=2092356)的 querystring 参数。

终结点需要两个参数以进行拼写更正：

|Param|值|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[必应拼写检查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 终结点密钥|

[必应拼写检查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 检测到错误时，将一并从终结点返回原始陈述、已更正陈述和预测。

#### <a name="v2-prediction-endpoint-response"></a>[V2 预测终结点响应](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 预测终结点响应](#tab/V3)

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
LUIS 中使用的必应拼写检查 API 不支持在拼写检查更改期间要忽略的单词列表。 如果需要允许字词或首字母缩写词的列表，请在将话语发送到 LUIS 进行意向预测之前在客户端应用程序中处理话语。

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>更改预生成 datetimeV2 实体的时区
当 LUIS 应用使用预生成的[datetimeV2](luis-reference-prebuilt-datetimev2.md)实体时，可在预测响应中返回 datetime 值。 请求的时区用于确定要返回的正确日期/时间。 如果请求在到达 LUIS 之前来自机器人或另一个集中式应用程序，则更正 LUIS 使用的时区。

### <a name="v3-prediction-api-to-alter-timezone"></a>用于更改时区的 V3 预测 API

在 V3 中， `datetimeReference`确定时区偏移量。 了解有关[V3 预测](luis-migration-api-v3.md#v3-post-body)的详细信息。

### <a name="v2-prediction-api-to-alter-timezone"></a>V2 预测 API 来更改时区
通过使用基于 API 版本的`timezoneOffset`参数将用户的时区添加到终结点来更正时区。 参数的值应为正数或负数，以分钟为单位，以更改时间。

#### <a name="v2-prediction-daylight-savings-example"></a>V2 预测夏令时示例
如果需要返回的预生成的 datetimeV2 来调整夏令时[，则应将查询字符串](https://go.microsoft.com/fwlink/?linkid=2092356)参数与 +/-值一起使用，以分钟为单位。

增加 60 分钟：

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

减去 60 分钟：

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>V2 预测 c # 代码确定正确的参数值

下面的 c # 代码使用[TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo)类的[timezoneinfo.findsystemtimezonebyid](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples)方法根据系统时间来确定正确的偏移值：

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过本教程更正拼写错误](luis-tutorial-bing-spellcheck.md)
