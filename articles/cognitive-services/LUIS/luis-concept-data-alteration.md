---
title: 了解 LUIS 中的数据更改概念 - Azure | Microsoft Docs
description: 了解如何在语言理解 (LUIS) 得出预测之前更改数据
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/26/2018
ms.author: v-geberr
ms.openlocfilehash: 4fb1a5542bb56bd853984e66198ebfbd189451f8
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266859"
---
# <a name="data-alterations"></a>数据更改
LUIS 提供在预测之前或预测期间操作陈述的方法。 

## <a name="correct-spelling-errors-in-utterance"></a>更正陈述中的拼写错误
LUIS 使用[必应拼写检查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 来更正陈述中的拼写错误。 LUIS 需要与该服务关联的密钥。 创建密钥，然后将密钥添加为[终结点](https://aka.ms/luis-endpoint-apis)的 querystring 参数。 

还可以通过[输入密钥](interactive-test.md#view-bing-spell-check-corrections-in-test-panel)更正“测试”面板中的拼写错误。 该密钥以浏览器中“测试”面板的会话变量形式保存。 在每个要更正拼写的浏览器会话中，将该密钥添加到“测试”面板。 

测试面板和终结点中的密钥使用情况将计入[密钥用量](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/)配额。 LUIS 实施必应拼写检查文本长度限制。 

终结点需要两个参数以进行拼写更正：

|Param|值|
|--|--|
|`spellCheck`|布尔值|
|`bing-spell-check-subscription-key`|[必应拼写检查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 订阅密钥|

[必应拼写检查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 检测到错误时，将一并从终结点返回原始陈述、已更正陈述和预测。

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
 
## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>更改预生成 datetimeV2 实体的时区
LUIS 应用使用预生成的 datetimeV2 实体时，可以在预测响应中返回日期/时间值。 请求的时区用于确定要返回的正确日期/时间。 如果请求在到达 LUIS 之前来自机器人或另一个集中式应用程序，则更正 LUIS 使用的时区。 

### <a name="endpoint-querystring-parameter"></a>终结点 querystring 参数
通过使用 `timezoneOffset` 参数将用户的时区添加到[终结点](https://aka.ms/luis-endpoint-apis)来更正时区。 要更改时间，则 `timezoneOffset` 的值应为正数或负数（以分钟为单位）。  

|Param|值|
|--|--|
|`timezoneOffset`|正数或负数（以分钟为单位）|

### <a name="daylight-savings-example"></a>夏令时示例
如果需要返回的预生成 datetimeV2 来调整夏令时，则对于该[终结点](https://aka.ms/luis-endpoint-apis)查询应使用值为正数/负数（以分钟为单位）的 `timezoneOffset` querystring 参数。

增加 60 分钟： 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

减去 60 分钟： 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C# 代码确定正确的 timezoneOffset 值
下面的 C# 代码使用 [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo?view=netframework-4.7.1) 类的 [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netframework-4.7.1#examples) 方法基于系统时间来确定正确的 `timezoneOffset`：

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

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions