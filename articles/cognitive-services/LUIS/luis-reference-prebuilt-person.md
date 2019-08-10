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
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a9da5089c9309ec06f29fd3e0868102cfa538049
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933457"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的 PersonName 预生成实体
预构建 personName 实体会检测人的姓名。 此实体已定型，因此不需要将包含 personName 的陈述示例添加到应用程序意向中。 英语和中文[区域性](luis-reference-prebuilt-entities.md)支持 personName 实体。

## <a name="resolution-for-personname-entity"></a>解析 personName 实体

### <a name="api-version-2x"></a>API 版本 2.x

以下示例显示了 builtin.personName 实体的解析。

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  },
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>预览版 API 版本 3.x

以下 JSON 的 `verbose` 参数设置为 `false`：

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ]
        }
    }
}
```

以下 JSON 的 `verbose` 参数设置为 `true`：

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
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
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

了解[电子邮件](luis-reference-prebuilt-email.md)、[数字](luis-reference-prebuilt-number.md)和[序号](luis-reference-prebuilt-ordinal.md)实体。 
