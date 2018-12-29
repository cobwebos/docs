---
title: PersonName 预生成实体
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中的 personName 预构建实体信息。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 31dce870e99cbe74e2795a3ba661b0caf92dd48e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140228"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的 PersonName 预生成实体
预构建 personName 实体会检测人的姓名。 此实体已定型，因此不需要将包含 personName 的陈述示例添加到应用程序意向中。 英语和中文[区域性](luis-reference-prebuilt-entities.md)支持 personName 实体。

## <a name="resolution-for-personname-entity"></a>解析 personName 实体
以下示例显示了 builtin.personName 实体的解析。

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
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

## <a name="next-steps"></a>后续步骤

了解[电子邮件](luis-reference-prebuilt-email.md)、[数字](luis-reference-prebuilt-number.md)和[序号](luis-reference-prebuilt-ordinal.md)实体。 