---
title: PersonName 预生成的实体-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中的 personName 预构建实体信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 768c719211e8a8f2133d3798343d076e795a3da0
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273424"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的 PersonName 预生成实体
预构建 personName 实体会检测人的姓名。 此实体已定型，因此不需要将包含 personName 的陈述示例添加到应用程序意向中。 英语和中文[区域性](luis-reference-prebuilt-entities.md)支持 personName 实体。

## <a name="resolution-for-personname-entity"></a>解析 personName 实体

为查询返回以下实体对象：

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[V3 响应](#tab/V3)


以下 JSON 是将 `verbose` 参数设置为 `false`的：

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/V3-verbose)
以下 JSON 是将 `verbose` 参数设置为 `true`的：

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[V2 响应](#tab/V2)

以下示例显示了 builtin.personName 实体的解析。

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>后续步骤

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

了解[电子邮件](luis-reference-prebuilt-email.md)、[数字](luis-reference-prebuilt-number.md)和[序号](luis-reference-prebuilt-ordinal.md)实体。
