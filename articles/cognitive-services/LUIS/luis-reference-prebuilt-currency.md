---
title: 货币预建的实体
titleSuffix: Azure
description: 本文包含了语言理解 (LUIS) 中的货币预构建实体信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 1dfa81ad7981578d4f296de1b421c7e064819718
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867294"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的货币预生成实体
预生成的货币实体检测中许多个面额和国家/地区，而不考虑 LUIS 应用程序区域性的货币。 此实体已定型，因此不需要将包含货币的陈述示例添加到应用程序意向中。 [许多语言区域](luis-reference-prebuilt-entities.md)都支持货币实体。 

## <a name="types-of-currency"></a>货币类型
货币托管在 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) GitHub 存储库中

## <a name="resolution-for-currency-entity"></a>货币实体的解析
以下示例显示了 **builtin.currency** 实体的解析。

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

了解 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[维度](luis-reference-prebuilt-dimension.md)和[电子邮件](luis-reference-prebuilt-email.md)实体。 
