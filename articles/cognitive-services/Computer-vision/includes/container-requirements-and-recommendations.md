---
title: 容器要求和建议。
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034486"
---
下表列出了为每个“识别文本”容器分配的最小和建议 CPU 核心数和内存。

| 容器 | 最低要求 | 建议 |TPS<br>(最小值, 最大值)|
|-----------|---------|-------------|--|
|识别文本|1核, 8 GB 内存, 0.5 TPS|2核, 8 GB 内存, 1 个 TPS|0.5、1|

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。
* TPS - 每秒事务数

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。