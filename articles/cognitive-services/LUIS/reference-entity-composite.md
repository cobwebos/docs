---
title: 复合实体类型 - LUIS
titleSuffix: Azure Cognitive Services
description: 复合实体由其他实体构成，例如预生成实体、简单实体、正则表达式实体和列表实体。 各种单独的实体构成整个实体。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695168"
---
# <a name="composite-entity"></a>复合实体 

复合实体由其他实体构成，例如预生成实体、简单实体、正则表达式实体和列表实体。 各种单独的实体构成整个实体。 

**如果数据具有以下特征，则非常适合使用此实体：**

* 彼此相关。 
* 在使用陈述的情况下彼此相关。
* 使用各种实体类型。
* 需要由客户端应用程序作为一个信息单元进行分组和处理。
* 包含需要机器学习的各种用户话语。

![复合实体](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>示例 JSON

考虑一个预生成的 `number` 和 `Location::ToLocation` 的复合实体，它包含以下话语：

`book 2 tickets to cairo`

注意数字 `2` 和 ToLocation `cairo` 之间有单词，这些单词不属于任何实体。 [LUIS](luis-reference-regions.md) 网站中的已标记话语中使用的绿色下划线指示复合实体。

![复合实体](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

复合实体返回在 `compositeEntities` 数组中，且该复合中的所有实体也都返回在 `entities` 数组中：

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```    

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

如果在查询字符串中设置了 `verbose=false`，则这是 JSON：

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

如果在查询字符串中设置了 `verbose=true`，则这是 JSON：

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 


|数据对象|实体名称|ReplTest1|
|--|--|--|
|预构建实体 - 数量|"builtin.number"|"2"|
|预生成实体 - GeographyV2|"Location::ToLocation"|cairo|

## <a name="next-steps"></a>后续步骤

在本[教程](luis-tutorial-composite-entity.md)中，添加**复合实体**来将提取的各种类型的数据捆绑到单个包含实体中。 通过捆绑数据，客户端应用程序可以轻松提取各种数据类型的相关数据。
