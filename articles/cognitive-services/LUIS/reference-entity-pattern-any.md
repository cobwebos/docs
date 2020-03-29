---
title: Pattern.any 实体类型 - LUIS
titleSuffix: Azure Cognitive Services
description: Patterns.any 是一种长度可变的占位符，仅在模式的模板话语中使用，用于标记实体的起始和结束位置。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75979166"
---
# <a name="patternany-entity"></a>Pattern.any 实体

Patterns.any 是一种长度可变的占位符，仅在模式的模板话语中使用，用于标记实体的起始和结束位置。  

需要在[模式](luis-how-to-model-intent-pattern.md)模板示例而不是意向用户示例中标记 Pattern.any 实体。

**在以下情况下，非常适合使用该实体：**

* 实体的末尾可能与话语的其余文本相混淆。

## <a name="usage"></a>使用情况

假设某个客户端应用程序需要基于标题搜索书籍，则 pattern.any 会提取完整的标题。 一个使用 pattern.any 进行这种书籍搜索的模板话语是 `Was {BookTitle} written by an American this year[?]`。

在下表中，每行包含话语的两个版本。 最上面的话语是 LUIS 最初看到的话语。 不清楚书名在哪里开始和在哪里结束。 最下面的话语使用 Pattern.any 实体来标记实体的开头和结尾。

|以粗体显示带实体的话语|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>《错把太太当成帽子的男人与其他医疗故事》是某位美国人在今年撰写的吗？****|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>今年一位美国人写的**青蛙睡衣中半睡半睡**吗？|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>**柠檬蛋糕的特殊悲伤：** 一个美国人今年写的小说吗？|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**我的口袋里有个沃克吗？** 是某位美国人在今年撰写的吗？|
||



## <a name="example-json"></a>示例 JSON

请考虑下列查询：

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

将嵌入式窗体名称提取为 Pattern.any：

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[V2 预测终结点响应](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 预测终结点响应](#tab/V3)

如果在查询字符串中设置了 `verbose=false`，则这是 JSON：

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

如果在查询字符串中设置了 `verbose=true`，则这是 JSON：

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
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

在[本教程](luis-tutorial-pattern.md)中，使用**Pattern.任何**实体从陈述格式良好的、数据末尾可能很容易与话语的剩余字词混淆的话语中提取数据。
