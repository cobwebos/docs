---
title: URL 预生成实体
titleSuffix: Azure
description: 本文包含语言理解 (LUIS) 中的 URL 预构建实体信息。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: f9e331abd31ef37e9214214118748ebda3eb9470
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225783"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的 URL 预生成实体
URL 实体提取带域名或 IP 地址的 URL。 此实体已定型，因此不需要将包含 URL 的示例陈述添加到应用程序。 仅在 `en-us` 区域性中支持 URL 实体。 

## <a name="types-of-urls"></a>URL 类型
URL 托管在 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) GitHub 存储库

## <a name="resolution-for-prebuilt-url-entity"></a>预构建 URL 实体解析
以下示例演示了 builtin.url 实体解析。

```json
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

了解[序号](luis-reference-prebuilt-ordinal.md)、[数字](luis-reference-prebuilt-number.md)和[温度](luis-reference-prebuilt-temperature.md)实体。
