---
title: LUIS 预构建实体存在时间参考 - Azure | Microsoft Docs
titleSuffix: Azure
description: 本文包含了语言理解 (LUIS) 中的存在时间预构建实体信息。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: f93acb8bece8c66c3ed7197f1c4530011aec3f29
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237394"
---
# <a name="age-entity"></a>存在时间实体
预构建存在时间实体同时采用数字和天、周、月以及年捕获存在时间值。 此实体已定型，因此不需要将包含存在时间的陈述示例添加到应用程序意向中。 [许多语言区域](luis-reference-prebuilt-entities.md)都支持存在时间实体。 

## <a name="types-of-age"></a>存在时间类型
存在时间托管在 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) Github 存储库中

## <a name="resolution-for-prebuilt-age-entity"></a>预构建存在时间实体的解析
以下示例显示了 **builtin.age** 实体的解析。

```JSON
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

了解[货币](luis-reference-prebuilt-currency.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md) 和[维度](luis-reference-prebuilt-dimension.md)实体。 