---
title: 序号 V2 预生成实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中序号 V2 预生成实体的信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 02bc6657126cb1cf241c2ca4668e62bd49608d4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491267"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的序号 V2 预生成实体
序号 V2 扩展了[序号](luis-reference-prebuilt-ordinal.md)以提供相关引用，如 `next`、`last` 和 `previous`。 这些无法使用序号预生成实体提取。

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>序号 V2 预生成实体的解析

为查询返回以下实体对象：

`what is the second to last choice in the list`

#### <a name="v3-responsetabv3"></a>[V3 响应](#tab/V3)

以下 JSON 的 `verbose` 参数设置为 `false`：

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

#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 详细响应](#tab/V3-verbose)

以下 JSON 的 `verbose` 参数设置为 `true`：

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
#### <a name="v2-responsetabv2"></a>[V2 响应](#tab/V2)

以下示例展示了 **builtin.ordinalV2** 实体的解析。

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

了解有关[V3 预测终结点](luis-migration-api-v3.md)的详细信息。

了解[百分比](luis-reference-prebuilt-percentage.md)、[电话号码](luis-reference-prebuilt-phonenumber.md)和[温度](luis-reference-prebuilt-temperature.md)实体。 
