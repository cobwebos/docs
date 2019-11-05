---
title: 货币预生成实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中的货币预构建实体信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 5b49dcc7e999757e119c399bdf01bed7cb312e02
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465046"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的货币预生成实体
预构建货币实体检测许多国家/地区/区域中许多面额的货币，无论 LUIS 应用语言区域是怎样的。 此实体已定型，因此不需要将包含货币的陈述示例添加到应用程序意向中。 [许多语言区域](luis-reference-prebuilt-entities.md)都支持货币实体。 

## <a name="types-of-currency"></a>货币类型
货币托管在 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) GitHub 存储库中

## <a name="resolution-for-currency-entity"></a>货币实体的解析

#### <a name="v3-responsetabv3"></a>[V3 响应](#tab/V3)

以下 JSON 的 `verbose` 参数设置为 `false`：

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 详细响应](#tab/V3-verbose)
以下 JSON 的 `verbose` 参数设置为 `true`：

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-responsetabv2"></a>[V2 响应](#tab/V2)

以下示例显示了 **builtin.currency** 实体的解析。

```json
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
```
* * * 

## <a name="next-steps"></a>后续步骤

了解有关[V3 预测终结点](luis-migration-api-v3.md)的详细信息。

了解 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[维度](luis-reference-prebuilt-dimension.md)和[电子邮件](luis-reference-prebuilt-email.md)实体。 
