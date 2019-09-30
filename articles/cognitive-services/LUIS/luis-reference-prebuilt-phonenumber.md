---
title: 电话号码预生成实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含语言理解 (LUIS) 中的电话号码预构建实体信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 9e237b09bd2b0ecc3e91e10697f6c025f73057da
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677442"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的电话号码预生成实体
`phonenumber` 实体提取各种包括国家/地区代码在内的电话号码。 此实体已定型，因此不需要将陈述示例添加到应用程序。 仅在 `en-us` 区域性中支持 `phonenumber` 实体。 

## <a name="types-of-a-phone-number"></a>电话号码的类型
`Phonenumber` 托管在 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) GitHub 存储库中

## <a name="resolution-for-this-prebuilt-entity"></a>针对此预生成实体的解决方法

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

以下示例演示了 builtin.phonenumber 实体解析。

```json
{
  "query": "my mobile is 1 (800) 642-7676",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

以下 JSON 的 `verbose` 参数设置为 `false`：

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

以下 JSON 的 `verbose` 参数设置为 `true`：

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ],
            "$instance": {
                "phonenumber": [
                    {
                        "type": "builtin.phonenumber",
                        "text": "1 (800) 642-7676",
                        "startIndex": 13,
                        "length": 16,
                        "score": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

* * * 

## <a name="next-steps"></a>后续步骤

了解有关[V3 预测终结点](luis-migration-api-v3.md)的详细信息。

了解有关[百分比](luis-reference-prebuilt-percentage.md)、[数字](luis-reference-prebuilt-number.md)和[温度](luis-reference-prebuilt-temperature.md)实体。 
