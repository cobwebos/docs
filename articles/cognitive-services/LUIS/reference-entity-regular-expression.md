---
title: 正则表达式实体类型-LUIS
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
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841211"
---
# <a name="regular-expression-entity"></a>正则表达式实体

正则表达式实体基于您提供的正则表达式模式提取实体。

正则表达式最适合用于原始话语文本。 不区分大小写，并忽略区域性变体。  完成字符级别而不是令牌级别的拼写检查更改后，会应用正则表达式匹配。 如果正则表达式过于复杂，例如使用了许多括号，则不能将表达式添加到模型。 使用部分而不是所有[.Net 正则表达式](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)库。

**实体适用于以下情况：**

* 数据的格式一致，并且其任何变体也是一致的。
* 正则表达式不需要 2 个级别以上的嵌套。

![正则表达式实体](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>使用注意事项

正则表达式的匹配项可能大于预期匹配的数目。 例如，数字字匹配（例如 `one` 和 `two`）。 例如，下面的正则表达式与其他数字一起匹配 `one`：

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

此正则表达式还匹配以这些数字结尾的所有单词，如 `phone`。 为了解决此类问题，请确保正则表达式匹配会考虑单词边界。 以下 regex 使用了此示例中使用字边界的 regex：

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>示例 JSON

当使用 `kb[0-9]{6}`作为正则表达式实体定义时，下面的 JSON 响应是一个示例查询文本，其中包含查询的返回正则表达式实体：

`When was kb123456 published?`：

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

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


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)


如果在查询字符串中设置 `verbose=false`，则这是 JSON：

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

如果在查询字符串中设置 `verbose=true`，则这是 JSON：

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

在本[教程](tutorial-regex-entity.md)中，使用**正则表达式**实体创建应用，以便从查询文本中提取一致格式的数据。