---
title: LUIS 预构建实体 URL 参考 - Azure | Microsoft Docs
titleSuffix: Azure
description: 本文包含语言理解 (LUIS) 中的 URL 预构建实体信息。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 1615d7e37119f185ebbb3bd674bc006d96d72e4d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030163"
---
# <a name="url-entity"></a>URL 实体
URL 实体提取带域名或 IP 地址的 URL。 此实体已定型，因此不需要将包含 URL 的示例陈述添加到应用程序。 仅在 `en-us` 区域性中支持 URL 实体。 

## <a name="types-of-urls"></a>URL 类型
URL 托管在 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) Github 存储库

## <a name="resolution-for-prebuilt-url-entity"></a>预构建 URL 实体解析
以下示例演示了 builtin.url 实体解析。

```JSON
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