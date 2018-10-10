---
title: PersonName预构建实体 - LUIS 参考
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中的 personName 预构建实体信息。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 0bc9d59fa44125429bfcb4e6ca9e68d93b81c04d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950567"
---
# <a name="personname-entity"></a>PersonName 实体
预构建 personName 实体会检测人的姓名。 此实体已定型，因此不需要将包含 personName 的陈述示例添加到应用程序意向中。 英语和中文[区域性](luis-reference-prebuilt-entities.md)支持 personName 实体。

## <a name="resolution-for-personname-entity"></a>解析 personName 实体
以下示例显示了 builtin.personName 实体的解析。

```JSON
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