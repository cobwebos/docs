---
title: 正则表达式实体类型
titleSuffix: Language Understanding - Azure Cognitive Services
description: 正则表达式最适合用于原始话语文本。 不区分大小写，并忽略区域性变体。  完成字符级别而不是令牌级别的拼写检查更改后，会应用正则表达式匹配。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: dfffea26ccd8dfcecea2c6a2e07234f3ab27f72b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480227"
---
# <a name="regular-expression-entity"></a>正则表达式实体 

正则表达式实体基于您提供的正则表达式模式提取实体。

正则表达式最适合用于原始话语文本。 不区分大小写，并忽略区域性变体。  完成字符级别而不是令牌级别的拼写检查更改后，会应用正则表达式匹配。 如果正则表达式过于复杂，例如使用了许多括号，则不能将表达式添加到模型。 使用部分但并非全部 [.NET Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) 库。 

**实体适用于以下情况:**

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

当使用`kb[0-9]{6}`作为正则表达式实体定义时, 下面的 JSON 响应是一个示例查询文本, 其中包含查询`When was kb123456 published?`的返回正则表达式实体:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

在本[教程](luis-quickstart-intents-regex-entity.md)中, 使用**正则表达式**实体创建应用, 以便从查询文本中提取一致格式的数据。