---
title: Pattern。任何实体类型
titleSuffix: Language Understanding - Azure Cognitive Services
description: Patterns.any 是一种长度可变的占位符，仅在模式的模板话语中使用，用于标记实体的起始和结束位置。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: f3e5bd068b5df99bdfabff0bfe18ccebff980fc6
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480161"
---
# <a name="patternany-entity"></a>Pattern.any 实体 

Patterns.any 是一种长度可变的占位符，仅在模式的模板话语中使用，用于标记实体的起始和结束位置。  

需要在[模式](luis-how-to-model-intent-pattern.md)模板示例而不是意向用户示例中标记 Pattern.any 实体。

**实体适用于以下情况:**

* 实体的末尾可能与话语的其余文本相混淆。 

## <a name="usage"></a>用法

假设某个客户端应用程序需要基于标题搜索书籍，则 pattern.any 会提取完整的标题。 一个使用 pattern.any 进行这种书籍搜索的模板话语是 `Was {BookTitle} written by an American this year[?]`。 

在下表中，每行包含话语的两个版本。 最常见的查询文本是 LUIS 最初如何看到查询文本。 本书的开头和结尾并不清楚。 底层查询文本使用模式。任何实体都可标记实体的开头和结尾。 

|带有以粗体显示的实体的查询文本|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>《错把太太当成帽子的男人与其他医疗故事》是某位美国人在今年撰写的吗？|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>《在宽大睡衣中半梦半睡》是某位美国人在今年撰写的吗？|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>《小说：柠檬蛋糕的特种忧伤》是某位美国人在今年撰写的吗？|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>《口袋里的毛怪！》 是某位美国人在今年撰写的吗？|
||

## <a name="example-json"></a>示例 JSON

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

在本[教程](luis-tutorial-pattern-any.md)中, 请使用**模式。任何**实体从最谈话中提取数据, 其中最谈话的格式正确, 并且数据的结尾可能会与查询文本中的其余单词轻松混淆。