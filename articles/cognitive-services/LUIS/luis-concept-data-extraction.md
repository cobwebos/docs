---
title: 数据提取-LUIS
description: 利用意向和实体从查询文本文本中提取数据。 了解可以从语言理解（LUIS）中提取的数据类型。
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 1c1a744c06e5347625fb96518bd809481ee797e5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716285"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>利用意向和实体从查询文本文本提取数据
使用 LUIS 可以从用户的自然语言陈述中获取信息。 信息以一种程序、应用程序或聊天机器人能够使用其来采取操作的方式进行提取。 在以下部分中，通过 JSON 示例了解从意向和实体返回了什么数据。

最难提取的数据是机器学习的数据，因为它不是确切的文本匹配。 机器学习[实体](luis-concept-entity-types.md)的数据提取需要作为[创作周期](luis-concept-app-iteration.md)的一部分，直到你确信已接收到所需的数据。

## <a name="data-location-and-key-usage"></a>数据位置和密钥用法
LUIS 从已发布的[终结点](luis-glossary.md#endpoint)提供数据。 HTTPS 请求（POST 或 GET）包含陈述以及一些可选配置，例如暂存或生产环境。

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 预测终结点请求](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 预测终结点请求](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *

在编辑 LUIS 应用时，`appID` 可在该 LUIS 应用的“设置”页上找到，也可在 URL 中找到（在 `/apps/` 之后）。 `subscription-key` 是用于查询应用的终结点密钥。 虽然可以在学习 LUIS 时使用免费的创作/初学者密钥，但是将终结点密钥更改为支持[所需 LUIS 用法](luis-boundaries.md#key-limits)的密钥非常重要。 `timezoneOffset` 的单位是分钟。

HTTPS 响应包含 LUIS 可基于当前发布的暂存或生产终结点的模型确定的所有意向和实体信息。 终结点 URL 位于 [LUIS](luis-reference-regions.md) 网站的“管理”部分的“密钥和终结点”页上。

## <a name="data-from-intents"></a>意向中的数据
主数据是评分最高的意向名称。 终结点响应为：

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *

|数据对象|数据类型|数据位置|值|
|--|--|--|--|
|Intent|String|topScoringIntent.intent|"GetStoreInfo"|

如果你的聊天机器人或 LUIS 调用应用根据多个意向评分做出决定，则返回所有意向分数。


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

设置 querystring 参数 `verbose=true`。 终结点响应为：

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

设置 querystring 参数 `show-all-intents=true`。 终结点响应为：

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *

意向按评分从高到低排序。

|数据对象|数据类型|数据位置|值|Score|
|--|--|--|--|:--|
|Intent|String|intents[0].intent|"GetStoreInfo"|0.984749258|
|Intent|String|intents[1].intent|"None"|0.0168218873|

如果添加预构建的域，则意向名称指示该域，例如 `Utilties` 或 `Communication` 以及意向：

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *

|域|数据对象|数据类型|数据位置|值|
|--|--|--|--|--|
|实用程序|Intent|String|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|通信|Intent|String|intents[1].intent|<b>Communication</b>.StartOver"|
||Intent|String|intents[2].intent|"None"|


## <a name="data-from-entities"></a>实体中的数据
大多数机器人和应用程序需要的都不止是意向名称。 此额外的可选数据来源于在陈述中发现的实体。 每种类型的实体返回有关匹配项的不同信息。

陈述中的单个单词或短语可以匹配多个实体。 在这种情况下，会返回每个匹配实体及其评分。

所有实体都返回在终结点响应中的“实体”数组中：

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *

## <a name="tokenized-entity-returned"></a>返回的切分后的实体

查看 LUIS 中的[令牌支持](luis-language-support.md#tokenization)。

## <a name="simple-entity-data"></a>简单实体数据

[简单实体](reference-entity-simple.md)是一种机器学习值。 它可以是一个单词或短语。

## <a name="composite-entity-data"></a>复合实体数据

[复合实体](reference-entity-composite.md)由其他实体（例如预生成实体、简单、正则表达式和列表实体）组成。 各种单独的实体构成整个实体。

## <a name="list-entity-data"></a>列表实体数据

[列表实体](reference-entity-list.md)表示固定的、封闭的相关单词集及其同义词。 LUIS 不会为列表实体发现更多值。 使用“建议”功能根据当前列表查看有关新词的建议。 如果存在多个具有相同值的列表实体，则终结点查询中会返回其中每个实体。

## <a name="prebuilt-entity-data"></a>预构建实体数据
[预构建](luis-concept-entity-types.md)实体是基于正则表达式匹配项、使用开源 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 项目发现的。 预构建实体返回在实体数组中，并使用前缀为 `builtin::` 的类型名称。 以下文本是一个示例陈述，其中包含返回的预构建实体：

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

如果没有 querystring 参数，`verbose=true`：

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

对于 querystring 参数，`verbose=true`：

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
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

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *
## <a name="regular-expression-entity-data"></a>正则表达式实体数据

[正则表达式实体](reference-entity-regular-expression.md)基于您提供的正则表达式模式提取实体。

## <a name="extracting-names"></a>提取名称
从陈述提取名称非常困难，因为名称几乎可以是字母和单词的任何组合。 根据要提取的名称类型，有若干选项。 以下建议不是规则，而是更多准则。

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>添加预构建的 PersonName 和 GeographyV2 实体

[PersonName](luis-reference-prebuilt-person.md) 和 [GeographyV2](luis-reference-prebuilt-geographyV2.md) 实体在某些[语言区域性](luis-reference-prebuilt-entities.md)中可用。

### <a name="names-of-people"></a>人的姓名

人的姓名可能会带有些许格式，具体取决于语言和区域性。 使用预生成的 **[personName](luis-reference-prebuilt-person.md)** 实体或具有名字和姓氏[角色](luis-concept-roles.md)的 **[简单实体](luis-concept-entity-types.md#simple-entity)** 。

如果你使用简单实体，请确保在查询文本的不同部分中使用第一个和最后一个名称（以不同的长度为最谈话）和最谈话（包括无意向的所有方法）提供的示例。 定期[查看](luis-how-to-review-endoint-utt.md)终结点陈述以标记未能正确预测的任何名称。

### <a name="names-of-places"></a>地名

位置名称已设置，如城市、县、省/市/自治区和国家/地区。 使用预生成的实体 **[geographyV2](luis-reference-prebuilt-geographyv2.md)** 提取位置信息。

### <a name="new-and-emerging-names"></a>新出现的名称

一些应用需要能够找到新出现的名称，例如产品或公司。 这些类型的名称是最难进行的数据提取类型。 从一个 **[简单的实体](luis-concept-entity-types.md#simple-entity)** 开始并添加[短语列表](luis-concept-feature.md)。 定期[查看](luis-how-to-review-endoint-utt.md)终结点陈述以标记未能正确预测的任何名称。

## <a name="pattern-roles-data"></a>模式角色数据
角色是实体间的上下文差别。


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

实体名称为 `Location`，具有两个角色： `Origin` 和 `Destination`。

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Employee",
    "startIndex": 5,
    "endIndex": 13,
    "score": 0.922820568,
    "role": ""
  },
  {
    "entity": "seattle",
    "type": "Location",
    "startIndex": 20,
    "endIndex": 26,
    "score": 0.948008537,
    "role": "Origin"
  },
  {
    "entity": "redmond",
    "type": "Location",
    "startIndex": 31,
    "endIndex": 37,
    "score": 0.7047979,
    "role": "Destination"
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

在 V3 中，**角色名称**是对象的主要名称。

实体名称为 `Location`，具有两个角色： `Origin` 和 `Destination`。

如果没有 querystring 参数，`verbose=true`：

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

对于 querystring 参数，`verbose=true`：

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *

## <a name="patternany-entity-data"></a>Pattern.any 实体数据

[Pattern。 any](reference-entity-pattern-any.md)是仅在模式的模板查询文本中使用的可变长度占位符，用于标记实体开始和结束的位置。

## <a name="sentiment-analysis"></a>情绪分析
如果配置了情绪分析，LUIS json 响应会包含情绪分析内容。 请在[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)文档中详细了解情绪分析。

### <a name="sentiment-data"></a>情绪数据
情绪数据是一个介于 0 到 1 之间的分数，指示数据的正面情绪（分数接近 1）或负面情绪（分数接近 0）。

当区域性为 `en-us` 时，响应为：

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

对于所有其他区域性，响应为：

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>关键短语提取实体数据
关键短语提取实体返回陈述中的关键短语，由[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)提供。


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

如果没有 querystring 参数，`verbose=true`：

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

对于 querystring 参数，`verbose=true`：

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
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

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *


## <a name="data-matching-multiple-entities"></a>匹配多个实体的数据

LUIS 返回在陈述中发现的所有实体。 因此，机器人可能需要基于这些结果进行决策。 一个陈述中可能包含很多实体：

`book me 2 adult business tickets to paris tomorrow on air france`

LUIS 终结点可在不同的实体中发现相同的数据。

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

不 `verbose=true` 作为查询字符串参数。

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

使用 `verbose=true` 作为查询字符串参数。


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *

## <a name="data-matching-multiple-list-entities"></a>匹配多个列表实体的数据

如果一个单词或短语与多个列表实体匹配，则终结点查询会返回每个列表实体。

对于查询 `when is the best time to go to red rock?`，且应用中的单词 `red` 出现在多个列表中，LUIS 会识别所有实体，并返回一组实体作为 JSON 终结点响应的一部分：

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

查询字符串中无 `verbose=true`：

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


在查询字符串中具有 `verbose=true`：

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

* * *

## <a name="next-steps"></a>后续步骤

请参阅[添加实体](luis-how-to-add-entities.md)，详细了解如何将实体添加到 LUIS 应用。
