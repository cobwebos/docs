---
title: 模型版本控制
titleSuffix: Azure Cognitive Services
description: 在 V3 终结点中指定模型版本
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 5a06e26e5f1640024e343c714db3df134422115c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488655"
---
可以通过第 3 版文本分析 API 选择在数据上使用的文本分析模型。 在请求中使用可选的 `model-version` 参数选择一个模型版本。 如果未指定此参数，API 会默认使用 `latest`（最新的稳定模型版本）。

可用模型版本：
* `2019-10-01` (`latest`)

来自 v3 终结点的每个响应都包含一个 `model-version` 字段，用于指定已使用的模型版本。

```json
{
    “documents”: […]
    “errors”: []
    “model-version”: “2019-10-01”
}
```
