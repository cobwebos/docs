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
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021039"
---
可以通过第 3 版文本分析 API 选择在数据上使用的文本分析模型。 在请求中使用可选的 `model-version` 参数选择一个模型版本。 如果未指定此参数，API 会默认使用 `latest`（最新的稳定模型版本）。

可用模型版本：
* `2019-10-01` (`latest`)

来自 v3 终结点的每个响应都包含一个 `model-version` 字段，用于指定已使用的模型版本。

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
