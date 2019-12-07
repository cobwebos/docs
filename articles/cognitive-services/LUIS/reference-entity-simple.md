---
title: 简单实体类型-LUIS
titleSuffix: Azure Cognitive Services
description: 简单实体介绍了机器学习的上下文中的单个概念。 使用简单实体来改善结果时添加短语列表。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 8b92aa6057c81ec9442372c5b85918cb92196d61
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894763"
---
# <a name="simple-entity"></a>简单实体

简单实体是描述单个概念的泛型实体，通过机器学习的上下文习得。 由于简单实体采用概括性的名称，例如公司名称、产品名称或其他类别的名称，因此，在使用简单实体时，请添加一个[短语列表](luis-concept-feature.md)，以提升所用名称的信号。

**实体适用于以下情况：**

* 数据格式不一致，但指示相同的事物。

![简单实体](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>示例 JSON

`Bob Jones wants 3 meatball pho`

在之前的陈述中，`Bob Jones` 被标记为一个简单的 `Customer` 实体。

从终结点返回的数据包括实体名称、从陈述中发现的文本、所发现文本的位置，以及评分：

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

如果在查询字符串中设置 `verbose=false`，则这是 JSON：

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
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

* * *

|数据对象|实体名称|Value|
|--|--|--|
|简单实体|`Customer`|`bob jones`|

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解模式语法](reference-pattern-syntax.md)