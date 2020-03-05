---
title: 电话号码预建实体-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含语言理解 (LUIS) 中的电话号码预构建实体信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270476"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的电话号码预构建实体
`phonenumber` 实体提取各种包括国家/地区代码在内的电话号码。 此实体已定型，因此不需要将陈述示例添加到应用程序。 仅在 `phonenumber` 区域性中支持 `en-us` 实体。

## <a name="types-of-a-phone-number"></a>电话号码的类型
从[识别器文本](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)GitHub 存储库管理 `Phonenumber`

## <a name="resolution-for-this-prebuilt-entity"></a>此预生成实体的解析

为查询返回以下实体对象：

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[V3 响应](#tab/V3)

以下 JSON 是将 `verbose` 参数设置为 `false`的：

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/V3-verbose)
以下 JSON 是将 `verbose` 参数设置为 `true`的：

```json
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
                "score": 1.0,
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

以下示例演示了 builtin.phonenumber 实体解析。

```json
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
```
* * *

## <a name="next-steps"></a>后续步骤

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

了解有关[百分比](luis-reference-prebuilt-percentage.md)、[数字](luis-reference-prebuilt-number.md)和[温度](luis-reference-prebuilt-temperature.md)实体。
