---
title: DatetimeV2 预生成实体-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含语言理解 (LUIS) 中的 datetimeV2 预生成实体信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/07/2020
ms.author: diberry
ms.openlocfilehash: 30132983f37323e798efd330f5cc8f15c0a9d2b6
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270733"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的 DatetimeV2 预生成实体

**datetimeV2** 预生成实体提取日期和时间值。 将以标准化格式解析这些值，使客户端程序能够使用它们。 当某条陈述包含不完整的日期或时间时，LUIS 会在终结点响应中包含过去的值和未来的值。 由于此实体已经过训练，因此不需要将包含 datetimeV2 的陈述示例添加到应用程序意向中。

## <a name="types-of-datetimev2"></a>datetimeV2 的类型
从[识别器文本](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml)GitHub 存储库管理 DatetimeV2。

## <a name="example-json"></a>示例 JSON

下面显示了下面的查询文本及其部分 JSON 响应。

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[V3 响应](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 响应](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|属性名称 |属性类型和说明|
|---|---|
|实体|**string** - 从类型为日期、时间、日期范围或时间范围的陈述中提取的文本。|
|type|**string** - [datetimeV2 的子类型](#subtypes-of-datetimev2)之一
|startIndex|**int** - 陈述中实体开始处的索引。|
|endIndex|**int** - 陈述中实体结束处的索引。|
|resolution|包含一个 `values` 数组，其中包含 1 个、2 个或 4 个[解析值](#values-of-resolution)。|
|end|时间或日期范围的结束值，格式与 `value` 相同。 仅当 `type` 为 `daterange`、`timerange` 或 `datetimerange` 时才使用|

* * *

## <a name="subtypes-of-datetimev2"></a>datetimeV2 的子类型

**datetimeV2** 预生成实体具有以下子类型，后面的表中提供了每个子类型的示例：
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


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
|mod|术语，用于说明如何使用 `before``after`之类的值。|
|type|子类型，可以是以下项之一： `datetime`、`date`、`time`、`daterange`、`timerange`、`datetimerange`、`duration`、`set`。|
|值|**可选。** Datetime 对象，格式为 yyyy-mm-dd （date），HH： MM： ss （time） yyyy-mm-dd HH： MM： ss （日期时间）。 如果 `type` 为 `duration`，则值是秒数（持续时间） <br/> 仅当 `type` 为 `datetime`、`date`、`time` 或 `duration 时才使用。|

## <a name="valid-date-values"></a>有效日期值

**datetimeV2** 支持以下范围内的日期：

| Min | Max |
|----------|-------------|
| 1900 年 1 月 1 日   | 2099 年 12 月 31 日 |

## <a name="ambiguous-dates"></a>歧义日期

如果日期可以是过去或未来的日期，则 LUIS 会提供这两个值。 包括年份中的月份和日期的陈述就是一个例子。

例如，给定以下查询文本：

`May 2nd`

* 如果今天的日期是 2017 年 5 月 3 日，则 LUIS 会提供“2017-05-02”和“2018-05-02”作为值。
* 如果今天的日期是 2017 年 5 月 1 日，则 LUIS 会提供“2016-05-02”和“2017-05-02”作为值。

以下示例显示了实体“5 月 2 日”的解析。 此解析假设今天的日期是 2017 年 5 月 2 日和 2018 年 5 月 1 日之间的某个日期。
在 `X` 字段中包含 `timex` 的字段是未在陈述中显式指定的日期的一部分。

## <a name="date-resolution-example"></a>日期解析示例


下面显示了下面的查询文本及其部分 JSON 响应。

`May 2nd`

#### <a name="v3-response"></a>[V3 响应](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 响应](#tab/2-3)

```json
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
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>数字日期的日期范围解析示例

`datetimeV2` 实体提取日期和时间范围。 `start` 和 `end` 字段指定范围的开始和结束。 对于查询文本 `May 2nd to May 5th`，LUIS 为当前年份和下一年提供**daterange**值。 在 `timex` 字段中，`XXXX` 值指示年份的歧义性。 `P3D` 指示时间段为三天。

下面显示了下面的查询文本及其部分 JSON 响应。

`May 2nd to May 5th`

#### <a name="v3-response"></a>[V3 响应](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 响应](#tab/3-3)

```json
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
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>星期日期的日期范围解析示例

下面的示例演示 LUIS 如何使用**datetimeV2**来解析查询文本 `Tuesday to Thursday`。 在此示例中，当前日期为 6 月 19 日。 LUIS 为当前日期前面和后面的日期范围包含 **daterange** 值。

下面显示了下面的查询文本及其部分 JSON 响应。

`Tuesday to Thursday`

#### <a name="v3-response"></a>[V3 响应](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 响应](#tab/4-3)

```json
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
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>歧义时间
如果时间或时间范围歧义，values 数组将包含两个 time 元素。 如果存在歧义的时间，则 values 包含 A.M. 和 P.M. 时间。

## <a name="time-range-resolution-example"></a>时间范围解析示例

API V3 中的 DatetimeV2 JSON 响应已更改。 以下示例演示 LUIS 如何使用 **datetimeV2** 来解析包含时间范围的陈述。

API V2 中的更改：
* 不再返回 `datetimeV2.timex.type` 属性，因为它在父级别返回，`datetimev2.type`。
* `datetimeV2.value` 属性已重命名为 `datetimeV2.timex`。

下面显示了下面的查询文本及其部分 JSON 响应。

`from 6pm to 7pm`

#### <a name="v3-response"></a>[V3 响应](#tab/5-1)

以下 JSON 是将 `verbose` 参数设置为 `false`的：

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/5-2)

以下 JSON 是将 `verbose` 参数设置为 `true`的：

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 响应](#tab/5-3)

```json
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

* * *

## <a name="time-resolution-example"></a>时间解析示例

下面显示了下面的查询文本及其部分 JSON 响应。

`8am`

#### <a name="v3-response"></a>[V3 响应](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 响应](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>已弃用的预生成日期时间

`datetime` 预生成实体已弃用，已由 datetimeV2 取代。

若要在 LUIS 应用中将 `datetime` 替换为 `datetimeV2`，请完成以下步骤：

1. 打开 LUIS Web 界面的“实体”窗格。
2. 删除 **datetime** 预生成实体。
3. 单击“添加预生成实体”
4. 选择“datetimeV2”并单击“保存”。

## <a name="next-steps"></a>后续步骤

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

了解[维度](luis-reference-prebuilt-dimension.md)、[电子邮件](luis-reference-prebuilt-email.md)实体和[数字](luis-reference-prebuilt-number.md)。

