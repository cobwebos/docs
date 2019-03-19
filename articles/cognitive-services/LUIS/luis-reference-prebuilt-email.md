---
title: LUIS 预构建实体电子邮件参考 - Azure | Microsoft Docs
titleSuffix: Azure
description: 本文包含了语言理解 (LUIS) 中的电子邮件预构建实体信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 4a48bb4a6e988d4352f957c6435a9c1bf0a3e5fb
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340110"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的电子邮件预生成实体
电子邮件提取包括陈述中的整个电子邮件地址。 此实体已定型，因此不需要将包含电子邮件的陈述示例添加到应用程序意向中。 只有 `en-us` 语言区域中支持电子邮件实体。 

## <a name="resolution-for-prebuilt-email"></a>预构建电子邮件解析
以下示例显示了 **builtin.email** 实体的解析。

```json
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55,
      "resolution": {
        "value": "patti.owens@microsoft.com"
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

了解[数字](luis-reference-prebuilt-number.md)、[序号](luis-reference-prebuilt-ordinal.md)和[百分比](luis-reference-prebuilt-percentage.md)。 
