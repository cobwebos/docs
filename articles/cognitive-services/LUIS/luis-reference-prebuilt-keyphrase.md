---
title: 关键短语预生成的实体-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中的 keyPhrase 预构建实体信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: e33b5c766781bc49310dfcae55c3d390a032b522
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933525"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的 keyPhrase 预构建实体
keyPhrase 从话语中提取各种关键短语。 不需要将包含 keyPhrase 的示例话语添加到应用程序。 keyPhrase 实体作为[文本分析](../text-analytics/overview.md)功能的一部分，在[许多区域性](luis-language-support.md#languages-supported)中都受支持。 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>预构建 keyPhrase 实体的解析

### <a name="api-version-2x"></a>API 版本 2.x

以下示例显示了 **builtin.keyPhrase** 实体的解析。

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

了解[百分比](luis-reference-prebuilt-percentage.md)、[数字](luis-reference-prebuilt-number.md)和[存在时间](luis-reference-prebuilt-age.md)实体。
