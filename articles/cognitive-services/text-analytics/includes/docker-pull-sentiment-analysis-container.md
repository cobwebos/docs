---
title: 情绪分析容器的 Docker 拉
titleSuffix: Azure Cognitive Services
description: 情绪分析容器的 Docker 拉取命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876955"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>情绪分析容器的 Docker 拉

使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器可用标记的完整说明，请参阅 Docker Hub 上[的情绪分析](https://go.microsoft.com/fwlink/?linkid=2018654)容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
