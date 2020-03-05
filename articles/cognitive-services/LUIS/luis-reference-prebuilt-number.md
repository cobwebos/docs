---
title: 编号预生成的实体-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中的数字预构建实体信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 85e3589d7467691e2b9a11879510ab980bbd875a
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273462"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的数字预生成实体
有许多方式使用数字值来量化、表达和描述信息片段。 本文仅包括了其中一些可能的示例。 LUIS 解释用户陈述中的变体并返回一致的数字值。 此实体已定型，因此不需要将包含数字的陈述示例添加到应用程序意向中。

## <a name="types-of-number"></a>数字类型
数字托管在 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) GitHub 存储库中

## <a name="examples-of-number-resolution"></a>数字解析示例

| 话语        | 实体   | 解决方法 |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      |
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS 在它返回的 JSON 响应的  **字段中包括 `builtin.number`** `resolution` 实体的已识别值。

## <a name="resolution-for-prebuilt-number"></a>预构建数字解析

为查询返回以下实体对象：

`order two dozen eggs`

#### <a name="v3-response"></a>[V3 响应](#tab/V3)

以下 JSON 是将 `verbose` 参数设置为 `false`的：

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/V3-verbose)

以下 JSON 是将 `verbose` 参数设置为 `true`的：

```json
"entities": {
    "number": [
        24
    ],
    "$instance": {
        "number": [
            {
                "type": "builtin.number",
                "text": "two dozen",
                "startIndex": 6,
                "length": 9,
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

下面的示例显示了来自 LUIS 的 JSON 响应，其中包括了对陈述“two dozen”的值 24 的解析。

```json
"entities": [
  {
    "entity": "two dozen",
    "type": "builtin.number",
    "startIndex": 6,
    "endIndex": 14,
    "resolution": {
      "subtype": "integer",
      "value": "24"
    }
  }
]
```
* * *

## <a name="next-steps"></a>后续步骤

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

了解[货币](luis-reference-prebuilt-currency.md)、[序号](luis-reference-prebuilt-ordinal.md)和[百分比](luis-reference-prebuilt-percentage.md)。
