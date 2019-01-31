---
title: LUIS 预构建实体电子邮件参考 - Azure | Microsoft Docs
titleSuffix: Azure
description: 本文包含了语言理解 (LUIS) 中的电子邮件预构建实体信息。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 080c4d08348265ea891dd02ff3aa2fe8ba2ad2f1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221481"
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
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

了解[数字](luis-reference-prebuilt-number.md)、[序号](luis-reference-prebuilt-ordinal.md)和[百分比](luis-reference-prebuilt-percentage.md)。 
