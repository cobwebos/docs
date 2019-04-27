---
title: 百分比预生成实体
titleSuffix: Azure
description: 本文包含了语言理解 (LUIS) 中的百分比预构建实体信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: b55aca79047a224a9e1a474afdabf43e2701872d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473138"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的百分比预生成实体
百分比数字可以显示为分数 `3 1/2` 或百分比 `2%`。 此实体已定型，因此不需要将包含百分比的陈述示例添加到应用程序意向中。 [许多语言区域](luis-reference-prebuilt-entities.md)都支持百分比实体。 

## <a name="types-of-percentage"></a>百分比类型
百分比托管在 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub 存储库中

## <a name="resolution-for-prebuilt-percentage-entity"></a>预构建百分比实体的解析
以下示例显示了 **builtin.percentage** 实体的解析。

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

了解[序号](luis-reference-prebuilt-ordinal.md)、[数字](luis-reference-prebuilt-number.md)和[温度](luis-reference-prebuilt-temperature.md)实体。 
