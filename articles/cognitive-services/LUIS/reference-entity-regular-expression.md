---
title: 正则表达式实体类型 - LUIS
titleSuffix: Azure Cognitive Services
description: 正则表达式最适合用于原始话语文本。 不区分大小写，并忽略区域性变体。  完成字符级别而不是令牌级别的拼写检查更改后，会应用正则表达式匹配。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74841211"
---
# <a name="regular-expression-entity"></a>正则表达式实体

正则表达式实体基于所提供的正则表达式模式提取实体。

正则表达式最适合用于原始话语文本。 不区分大小写，并忽略区域性变体。  完成字符级别而不是令牌级别的拼写检查更改后，会应用正则表达式匹配。 如果正则表达式过于复杂，例如使用了许多括号，则不能将表达式添加到模型。 使用部分但不是所有[.NET Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)库。

**在以下情况下，非常适合使用该实体：**

* 数据的格式一致，并且其任何变体也是一致的。
* 正则表达式不需要 2 个级别以上的嵌套。

![正则表达式实体](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>使用注意事项

正则表达式可能匹配超出预期的匹配。 例如，数字单词匹配，例如 `one` 和 `two`。 例如，下面的正则表达式匹配数字 `one` 以及其他数字：

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

此正则表达式还匹配以这些数字结尾的任何单词，如 `phone`。 为了解决这样的问题，请确保正则表达式匹配考虑到单词边界。 此示例中使用单词边界的正则表达式用于以下正则表达式：

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>示例 JSON

如果将 `kb[0-9]{6}` 用作正则表达式实体定义，则下面的 JSON 响应就是一个示例话语，其中包含查询返回的正则表达式实体：

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2 预测终结点响应](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 预测终结点响应](#tab/V3)


如果在查询字符串中设置了 `verbose=false`，则这是 JSON：

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

如果在查询字符串中设置了 `verbose=true`，则这是 JSON：

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>后续步骤

在[本教程中](tutorial-regex-entity.md)，创建一个应用，以便使用**正则表达式**实体从陈述中提取一致格式的数据。