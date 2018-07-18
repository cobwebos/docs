---
title: LUIS 预生成实体 datetimeV2 参考 - Azure | Microsoft Docs
titleSuffix: Azure
description: 本文包含语言理解 (LUIS) 中的 datetimeV2 预生成实体信息。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 261f6f27c39c280efdcd070888d735374a473c85
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321569"
---
# <a name="datetimev2-entity"></a>DatetimeV2 实体

**datetimeV2** 预生成实体提取日期和时间值。 将以标准化格式解析这些值，使客户端程序能够使用它们。 当某条陈述包含不完整的日期或时间时，LUIS 会在终结点响应中包含过去的值和未来的值。 由于此实体已经过训练，因此不需要将包含 datetimeV2 的陈述示例添加到应用程序意向中。 

## <a name="types-of-datetimev2"></a>datetimeV2 的类型
通过 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) Github 存储库管理 DatetimeV2

## <a name="example-json"></a>示例 JSON 
以下示例 JSON 响应包含子类型为 `datetime` 的 `datetimeV2` 实体。 有关 datetimeV2 实体的其他类型示例，请参阅 [datetimeV2 的子类型](#subtypes-of-datetimev2)</a>。

```JSON
"entities": [
  {
    "entity": "8am on may 2nd 2017",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 15,
    "endIndex": 30,
    "resolution": {
      "values": [
        {
          "timex": "2017-05-02T08",
          "type": "datetime",
          "value": "2017-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>JSON 属性说明

|属性名称 |属性类型和说明|
|---|---|
|实体|**string** - 从类型为日期、时间、日期范围或时间范围的陈述中提取的文本。|
|type|**string** - [datetimeV2 的子类型](#subtypes-of-datetimev2)之一
|startIndex|**int** - 陈述中实体开始处的索引。|
|endIndex|**int** - 陈述中实体结束处的索引。|
|resolution|包含一个 `values` 数组，其中包含 1 个、2 个或 4 个[解析值](#values-of-resolution)。|
|end|时间或日期范围的结束值，格式与 `value` 相同。 仅当 `type` 为 `daterange`、`timerange` 或 `datetimerange` 时才使用|

## <a name="subtypes-of-datetimev2"></a>datetimeV2 的子类型

**datetimeV2** 预生成实体具有以下子类型，后面的表中提供了每个子类型的示例：
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>解析值
* 如果陈述中的日期或时间已完全指定且没有歧义，则数组包含一个元素。
* 如果 datetimeV2 值歧义，则数组包含两个元素。 歧义性包括缺少特定的年份、时间或时间范围。 有关示例，请参阅[歧义日期](#ambiguous-dates)。 当时间在 A.M. 或 P.M. 方面存在歧义时， 会包含这两个值。
* 如果陈述包含两个有歧义的元素，则数组包含四个元素。 这种歧义性包括具有以下特征的元素：
  * 日期或日期范围在年份方面存在歧义
  * 日期或日期范围在 A.M. 或 P.M. 方面存在歧义 例如，4 月 3 日 3:00。

`values` 数组的每个元素可以包含以下字段： 

|属性名称|属性说明|
|--|--|
|timex|遵循 [ISO 8601 标准](https://en.wikipedia.org/wiki/ISO_8601)的、以 TIMEX 格式表示的时间、日期或日期范围，以及使用 TimeML 语言的注释的 TIMEX3 属性。 [TIMEX 准则](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf)中描述了此注释。|
|type|子类型，可以是以下项之一：datetime、date、time、daterange、timerange、datetimerange、duration、set。|
|值|**可选。** 采用 yyyy:MM:dd（日期）、HH:mm:ss（时间）、yyyy:MM:dd HH:mm:ss（日期时间）格式的日期时间对象。 如果 `type` 为 `duration`，则值是秒数（持续时间） <br/> 仅当 `type` 为 `datetime`、`date`、`time` 或 `duration 时才使用。|

## <a name="valid-date-values"></a>有效日期值

**datetimeV2** 支持以下范围内的日期：

| Min | Max |
|----------|-------------|
| 1900 年 1 月 1 日   | 2099 年 12 月 31 日 |

## <a name="ambiguous-dates"></a>歧义日期

如果日期可以是过去或未来的日期，则 LUIS 会提供这两个值。 包括年份中的月份和日期的陈述就是一个例子。  

例如，假设陈述为“5 月 2 日”：
* 如果今天的日期是 2017 年 5 月 3 日，则 LUIS 会提供“2017-05-02”和“2018-05-02”作为值。 
* 如果今天的日期是 2017 年 5 月 1 日，则 LUIS 会提供“2016-05-02”和“2017-05-02”作为值。

以下示例显示了实体“5 月 2 日”的解析。 此解析假设今天的日期是 2017 年 5 月 2 日和 2018 年 5 月 1 日之间的某个日期。
在 `timex` 字段中包含 `X` 的字段是未在陈述中显式指定的日期的一部分。

```JSON
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2017-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2018-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>数字日期的日期范围解析示例

`datetimeV2` 实体提取日期和时间范围。 `start` 和 `end` 字段指定范围的开始和结束。 对于陈述“5 月 2 日到 5 月 5 日”，LUIS 会提供当前年份和下一年份的 **daterange** 值。 在 `timex` 字段中，`XXXX` 值指示年份的歧义性。 `P3D` 指示时间段为三天。

```JSON
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>星期日期的日期范围解析示例

以下示例演示 LUIS 如何使用 **datetimeV2** 来解析陈述“星期二到星期四”。 在此示例中，当前日期为 6 月 19 日。 LUIS 为当前日期前面和后面的日期范围包含 **daterange** 值。

```JSON
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-13",
            "end": "2017-06-15"
          },
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-20",
            "end": "2017-06-22"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>歧义时间
如果时间或时间范围歧义，values 数组将包含两个 time 元素。 如果存在歧义的时间，则 values 包含 A.M. 和 P.M. 时间。

## <a name="time-range-resolution-example"></a>时间范围解析示例

以下示例演示 LUIS 如何使用 **datetimeV2** 来解析包含时间范围的陈述。

```
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

## <a name="deprecated-prebuilt-datetime"></a>已弃用的预生成日期时间

`datetime` 预生成实体已弃用，已由 [`datetimeV2`](#builtindatetimev2) 取代。 

若要在 LUIS 应用中将 `datetime` 替换为 `datetimeV2`，请完成以下步骤：

1. 打开 LUIS Web 界面的“实体”窗格。 
2. 删除 **datetime** 预生成实体。
3. 单击“添加预生成实体”
4. 选择“datetimeV2”并单击“保存”。

## <a name="next-steps"></a>后续步骤

了解[维度](luis-reference-prebuilt-dimension.md)、[电子邮件](luis-reference-prebuilt-email.md)实体和[数字](luis-reference-prebuilt-number.md)。 

