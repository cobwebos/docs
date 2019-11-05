---
title: 维度预生成实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中维度预构建实体的信息。
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 6699b1617ccd1fef9a507e71fdd73a02b0e98bea
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465038"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的维度预生成实体
预构建维度实体检测各种类型的维度，无论 LUIS 应用语言区域是怎样的。 此实体已定型，因此不需要将包含维度的陈述示例添加到应用程序意向中。 [许多语言区域](luis-reference-prebuilt-entities.md)都支持维度实体。 

## <a name="types-of-dimension"></a>维度类型

从[识别器文本](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)GitHub 存储库管理维度。

## <a name="resolution-for-dimension-entity"></a>维度实体的解析

为查询返回以下实体对象：

`10 1/2 miles of cable`

#### <a name="v3-responsetabv3"></a>[V3 响应](#tab/V3)

以下 JSON 的 `verbose` 参数设置为 `false`：

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 详细响应](#tab/V3-verbose)
以下 JSON 的 `verbose` 参数设置为 `true`：

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

以下示例显示了 **builtin.dimension** 实体的解析。

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * * 

## <a name="next-steps"></a>后续步骤

了解有关[V3 预测终结点](luis-migration-api-v3.md)的详细信息。

了解[电子邮件](luis-reference-prebuilt-email.md)、[数字](luis-reference-prebuilt-number.md)和[序号](luis-reference-prebuilt-ordinal.md)实体。 
