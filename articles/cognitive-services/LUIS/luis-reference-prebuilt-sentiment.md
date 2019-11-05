---
title: 情绪分析 - LUIS
titleSuffix: Azure Cognitive Services
description: 如果配置了情绪分析，LUIS json 响应会包含情绪分析内容。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: a91246e4a4b3dc98bf11d2b014478fd40252f14b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508019"
---
# <a name="sentiment-analysis"></a>情绪分析
如果配置了情绪分析，LUIS json 响应会包含情绪分析内容。 请在[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)文档中详细了解情绪分析。


## <a name="resolution-for-sentiment"></a>情绪的解析

情绪数据是一个 0 到 1 的分数，指示数据的正面情绪（分数接近 1）或负面情绪（分数接近 0）。

#### <a name="english-languagetabenglish"></a>[英语](#tab/english)

当区域性为 `en-us` 时，响应为：

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languagestabother-languages"></a>[其他语言](#tab/other-languages)

对于所有其他区域性，响应为：

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * * 

## <a name="next-steps"></a>后续步骤

了解有关[V3 预测终结点](luis-migration-api-v3.md)的详细信息。

