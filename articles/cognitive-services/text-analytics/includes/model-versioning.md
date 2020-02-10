---
title: 模型版本控制
titleSuffix: Azure Cognitive Services
description: 在 V3 终结点中指定模型版本
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77089034"
---
使用文本分析 API 的版本 3 可以选择数据的最新模型版本。 使用可选参数 `model-version` 选择请求所需的模型版本。 如果未指定此参数，API 会默认为 `latest`（最新的稳定版）。 尽管可以在任何请求中使用最新的模型版本，但在每个版本中只更新了某些功能。 下表说明了在每个模型版本中更新了哪些功能：

| 模型版本           | 已更新功能         | 以下功能的最新版：           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | 实体识别                      | 实体识别                      |
| `2019-10-01`            | 实体识别、情绪分析  | 语言检测、关键短语提取、情绪分析|


来自 v3 终结点的每个响应都包含一个 `model-version` 字段，用于指定已使用的模型版本。

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
有关这些模型版本的更新的详细信息，请参阅[新增功能](../whats-new.md)。
