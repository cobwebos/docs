---
title: 序号 V2 预生成的实体-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含在语言理解中的序号为 V2 的实体信息（LUIS）。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270486"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的序号 V2 预构建实体
[序数 V2 number 用于提供](luis-reference-prebuilt-ordinal.md)相对引用，如 `next`、`last`和 `previous`。 不使用序号预生成实体来提取这些。

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>预生成的序号 V2 实体的解析

为查询返回以下实体对象：

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3 响应](#tab/V3)

以下 JSON 是将 `verbose` 参数设置为 `false`的：

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/V3-verbose)

以下 JSON 是将 `verbose` 参数设置为 `true`的：

```json
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
```
#### <a name="v2-response"></a>[V2 响应](#tab/V2)

下面的示例演示**ordinalV2**实体的解析。

```json
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
```
* * *

## <a name="next-steps"></a>后续步骤

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

了解[百分比](luis-reference-prebuilt-percentage.md)、[电话号码](luis-reference-prebuilt-phonenumber.md)和[温度](luis-reference-prebuilt-temperature.md)实体。
