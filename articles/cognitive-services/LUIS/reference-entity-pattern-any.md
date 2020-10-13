---
title: Pattern.any 实体类型 - LUIS
titleSuffix: Azure Cognitive Services
description: Patterns.any 是一种长度可变的占位符，仅在模式的模板话语中使用，用于标记实体的起始和结束位置。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: ec23be3709cebc534c059a21c52452abff683b18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542200"
---
# <a name="patternany-entity"></a>Pattern.any 实体

Patterns.any 是一种长度可变的占位符，仅在模式的模板话语中使用，用于标记实体的起始和结束位置。  

需要在[模式](luis-how-to-model-intent-pattern.md)模板示例而不是意向用户示例中标记 Pattern.any 实体。

**在以下情况下，非常适合使用此实体：**

* 实体的末尾可能与话语的其余文本相混淆。

## <a name="usage"></a>使用情况

假设某个客户端应用程序需要基于标题搜索书籍，则 pattern.any 会提取完整的标题。 一个使用 pattern.any 进行这种书籍搜索的模板话语是 `Was {BookTitle} written by an American this year[?]`。

在下表中，每行包含话语的两个版本。 最上面的话语是 LUIS 最初看到的话语。 不清楚书名在哪里开始和在哪里结束。 最下面的话语使用 Pattern.any 实体来标记实体的开头和结尾。

|以粗体显示带实体的话语|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>《错把太太当成帽子的男人与其他医疗故事》是某位美国人在今年撰写的吗？ |
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>《在宽大睡衣中半梦半睡》是某位美国人在今年撰写的吗？ |
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>《小说：柠檬蛋糕的特种忧伤》  是某位美国人在今年撰写的吗？|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>《口袋里的毛怪！》  是某位美国人在今年撰写的吗？|
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

在本 [教程](luis-tutorial-pattern.md)中，请使用 **模式。任何** 实体从最谈话中提取数据，其中最谈话的格式正确，并且数据的结尾可能会与查询文本中的其余单词轻松混淆。
