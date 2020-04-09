---
title: 键短语提取容器的 Docker 拉拔
titleSuffix: Azure Cognitive Services
description: 键短语提取容器的 Docker 拉取命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876961"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>键短语提取容器的 Docker 拉拔

使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器可用标记的完整说明，请参阅 Docker Hub 上的["关键短语提取"](https://go.microsoft.com/fwlink/?linkid=2018757)容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
