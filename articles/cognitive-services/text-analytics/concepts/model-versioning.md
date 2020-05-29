---
title: 在文本分析 v3 中指定模型版本
titleSuffix: Azure Cognitive Services
description: 了解如何指定用于数据的文本分析 API 模型。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/21/2020
ms.author: aahi
ms.openlocfilehash: 9431ff862dd987a1a806087053014e7c880bf801
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2020
ms.locfileid: "84143300"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>文本分析 API 中的模型版本控制

使用版本3的文本分析 API 可以选择用于数据的模型版本。 使用可选 `model-version` 参数在 API 请求中选择模型的版本。 例如： `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01` 。 如果未指定此参数，则 API 将默认为最新稳定版本。 

## <a name="available-versions"></a>可用版本

使用下表来确定每个终结点支持的模型版本。


| 终结点                        | 支持的版本                       | 最新版本 |
|---------------------------------|------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`               | `2020-04-01`   |
| `/languages`                    | `2019-10-01`                             | `2019-10-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`               | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/keyphrases`                   | `2019-10-01`                             | `2019-10-01`   |


可在[新增功能](../whats-new.md)中找到有关这些模型的更新的详细信息。

## <a name="next-steps"></a>后续步骤

* [文本分析概述](../overview.md)
* [情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [实体识别](../how-tos/text-analytics-how-to-entity-linking.md)