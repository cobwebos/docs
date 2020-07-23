---
title: 针对情绪分析容器的 Docker 请求
titleSuffix: Azure Cognitive Services
description: 适用于情绪分析容器的 Docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588358"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>针对情绪分析 v3 容器的 Docker 拉取

情绪分析容器 v3 容器提供多种语言版本。 若要下载英文容器的容器，请使用以下命令。 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
```

若要下载其他语言的容器，请 `en` 将替换为以下语言代码之一。 

| 文本分析容器 | 语言代码 |
|--|--|
| 英语 | `en` |
| 西班牙语 | `es` |
| 法语 | `fr` |
| 意大利语 | `it` |
| 德语 | `de` |
| 中文-简体 | `zh` |
| 中文-繁体 | `zht` |
| 日语 | `ja` |
| 葡萄牙语 | `pt` |
| 荷兰语 | `nl` |

有关文本分析容器的可用标记的完整说明，请参阅[Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654)。