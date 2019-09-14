---
title: 针对关键短语提取容器的 Docker 请求
titleSuffix: Azure Cognitive Services
description: 适用于关键短语提取容器的 Docker pull 命令
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966751"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>针对关键短语提取容器的 Docker 请求

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器的可用标记的完整说明，请参阅 Docker 中心上的[关键短语提取](https://go.microsoft.com/fwlink/?linkid=2018757)容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
