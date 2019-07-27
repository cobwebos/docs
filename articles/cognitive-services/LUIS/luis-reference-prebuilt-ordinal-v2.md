---
title: 序号 V2 预生成的实体-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中序号 V2 预生成实体的信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 972f75fd1c977e79a2fa70c44bb3069e2c69a2c5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563411"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的序号 V2 预生成实体
序号 V2 扩展了[序号](luis-reference-prebuilt-ordinal.md)以提供相关引用，如 `next`、`last` 和 `previous`。 这些无法使用序号预生成实体提取。

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>序号 V2 预生成实体的解析

### <a name="api-version-2x"></a>API 版本 2.x

以下示例展示了 **builtin.ordinalV2** 实体的解析。

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

了解[百分比](luis-reference-prebuilt-percentage.md)、[电话号码](luis-reference-prebuilt-phonenumber.md)和[温度](luis-reference-prebuilt-temperature.md)实体。 
