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
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876955"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>针对情绪分析容器的 Docker 请求

使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器的可用标记的完整说明，请参阅 Docker 中心上的[情绪分析](https://go.microsoft.com/fwlink/?linkid=2018654)容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
