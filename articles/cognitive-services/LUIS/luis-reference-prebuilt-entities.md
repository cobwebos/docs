---
title: LUIS 预构建实体参考 - Azure | Microsoft Docs
titleSuffix: Azure
description: 本文包含了语言理解 (LUIS) 中包括的预构建实体的列表。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/11/2018
ms.author: diberry
ms.openlocfilehash: 731ac279b4b0c162809d8e0638b9337924859b3d
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238823"
---
# <a name="entities-per-culture"></a>每个语言区域的实体

语言理解 (LUIS) 提供了预构建的实体。 当应用程序中包括预构建实体时，LUIS 会在终结点响应中包括对应的实体预测。 所有陈述示例都标记有实体。 **无法**修改预构建实体的行为。 除非另行说明，预构建实体在所有 LUIS 应用程序区域设置（语言区域）中都可用。 下表显示了每个语言区域支持的预构建实体。

预构建实体   |   英语(美国)<br>```En-us```   |   法语(法国)<br>```fr-FR```   |   意大利语(意大利)<br>```it-IT```   |   西班牙语(西班牙)<br>```es-ES```   |   中文<br>```zh-CN```   |   德语<br>```de-DE```   |   葡萄牙语(巴西)<br>```pt-BR```   |   日语(日本)<br>```ja-JP```   |   韩语(韩国)<br>```ko-kr```   | 法语(加拿大)<br>```fr-CA```   |   西班牙语(墨西哥)<br>```es-MX```   |   荷兰语(荷兰)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[存在时长](luis-reference-prebuilt-age.md)：<br>year<br>月份<br>week<br>day   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[货币](luis-reference-prebuilt-currency.md)：<br>美元<br>分数单位（示例：便士）  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>time<br>时间范围   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[维度](luis-reference-prebuilt-dimension.md)：<br>卷<br>面积<br>重量<br>信息（示例：位/字节）<br>长度（示例：米）<br>速度（示例：英里每小时）  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[电子邮件](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[keyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   |   ✔   |   ✔   |   ✔   |   -   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |
[数字](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[序号](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[百分比](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[电话号码](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[温度](luis-reference-prebuilt-temperature.md)：<br>华氏温度<br>开氏温度<br>兰金温度<br>德利尔温度<br>摄氏温度   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[URL](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

请参阅有关[已废弃的预构建实体](luis-reference-prebuilt-deprecated.md)的说明。

KeyPhrase 不适用于葡萄牙语（巴西）的所有子区域 - ```pt-BR```。

<!--
## Examples of prebuilt entities in en-us culture
The following table lists prebuilt entities with example data and the return values.

Prebuilt entity   |   Example utterance   |   JSON
------|------|------|
 ```builtin.age```   |   ```100 year old```   |```{ "type": "builtin.age", "entity": "100 year old" }```|  
 ```builtin.age```   |   ```19 years old```   |```{ "type": "builtin.age", "entity": "19 years old" }```|
 ```builtin.currency```     |   ```1000.00 US dollars```   |```{ "type": "builtin.currency", "entity": "1000.00 us dollars" }```
 ```builtin.currency```     |   ```$ 67.5 B```   |```{ "type": "builtin.currency", "entity": "$ 67.5" }```|
 ```builtin.datetimeV2``` | See [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) | See [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) |
 ```builtin.dimension```     |   ```2 miles```   |```{ "type": "builtin.dimension", "entity": "2 miles" }```|
 ```builtin.dimension```     |  ```650 square kilometers```   |```{ "type": "builtin.dimension", "entity": "650 square kilometers" }```|
 ```builtin.email```     |  ```patti.owens@microsoft.com```   |```{ "type": "builtin.email", "entity": "patti.owens@microsoft.com" }```|
 ```builtin.number```     |  ```ten```   |``` { "type": "builtin.number", "entity": "ten" } ```|
 ```builtin.number```     |   ```3.1415```   |```  { "type": "builtin.number", "entity": "3 . 1415" }``` |
 ```builtin.ordinal```     |   ```first```   |```{ "type": "builtin.ordinal", "entity": "first" }``` |
 ```builtin.ordinal```     |   ```10th```   | ```{ "type": "builtin.ordinal", "entity": "10th" }``` |  
 ```builtin.percentage```   |   ```The stock price increase by 7 $ this year```   |```{ "type": "builtin.percentage", "entity": "7 %" }```|
 ```builtin.phonenumber```   |   ```my mobile is 00 44 161 1234567```   |```{ "type": "builtin.phonenumber", "entity": "00 44 161 1234567" }```|
 ```builtin.temperature```     |   ```10 degrees celsius```   | ```{ "type": "builtin.temperature", "entity": "10 degrees celcius" }```|   
 ```builtin.temperature```     |   ```78 F```   |```{ "type": "builtin.temperature", "entity": "78 f" }```|
 ```builtin.url```     |   ```http://www.luis.ai is a great cognitive service```   |```{ "type": "builtin.url", "entity": "http://www.luis.ai" }```|
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>为预构建实体语言区域做贡献
预构建实体是在 Recognizers-Text 开发源代码项目中开发的。 请为该项目[贡献自己的力量](https://github.com/Microsoft/Recognizers-Text)。 该项目包括每个语言区域的货币的示例。 

## <a name="next-steps"></a>后续步骤

了解[数字](luis-reference-prebuilt-number.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md) 和[货币](luis-reference-prebuilt-currency.md)实体。 