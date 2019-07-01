---
title: 序号 V2 预生成的实体
titleSuffix: Language Understanding - Azure Cognitive Services
description: 本文包含序号 V2 预生成的实体信息在语言理解 (LUIS)。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 862b962f5642e01d7ed8250f49d51a6132447083
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486136"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>序号 V2 LUIS 应用程序的预建的实体
序号 V2 数量展开[序号](luis-reference-prebuilt-ordinal.md)提供相对引用，例如`next`， `last`，和`previous`。 这些将不会提取使用序号预生成的实体。

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>预生成的序号 V2 实体解析

### <a name="api-version-2x"></a>API 版本 2.x

下面的示例演示的分辨率**builtin.ordinalV2**实体。

```json
{
    "query": "what is the second to last choice in the list",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.823669851
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.823669851
        }
    ],
    "entities": [
        {
            "entity": "the second to last",
            "type": "builtin.ordinalV2.relative",
            "startIndex": 8,
            "endIndex": 25,
            "resolution": {
                "offset": "-1",
                "relativeTo": "end"
            }
        }
    ]
}
```

### <a name="preview-api-version-3x"></a>预览版 API 版本 3.x

以下 JSON 的 `verbose` 参数设置为 `false`：

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ]
        }
    }
}
```

以下 JSON 的 `verbose` 参数设置为 `true`：

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ],
            "$instance": {
                "ordinalV2": [
                    {
                        "type": "builtin.ordinalV2.relative",
                        "text": "the second to last",
                        "startIndex": 8,
                        "length": 18,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

了解如何[百分比](luis-reference-prebuilt-percentage.md)，[电话号码](luis-reference-prebuilt-phonenumber.md)，并[温度](luis-reference-prebuilt-temperature.md)实体。 
