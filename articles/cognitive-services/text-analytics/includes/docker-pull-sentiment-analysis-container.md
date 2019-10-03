---
title: 针对情绪分析容器的 Docker 请求
titleSuffix: Azure Cognitive Services
description: 适用于情绪分析容器的 Docker pull 命令
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966659"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>针对情绪分析容器的 Docker 请求

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器的可用标记的完整说明，请参阅 Docker 中心上的[情绪分析](https://go.microsoft.com/fwlink/?linkid=2018654)容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
