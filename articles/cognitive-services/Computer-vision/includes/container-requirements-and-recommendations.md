---
title: 容器要求和建议
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481755"
---
> [!NOTE]
> 要求和建议基于每秒单个请求的基准，使用 8 MB 的扫描业务信函图像，其中包含29行，总共803个字符。

下表描述了每个读取容器的最小和建议的资源分配。

| 容器 | 最小值 | 建议 |TPS<br>(最小值, 最大值)|
|-----------|---------|-------------|--|
| 读取 | 1个内核，8 GB 内存，0.24 TPS | 8核，16 GB 内存，1.17 TPS | 0.24、1.17 |

* 每个核心至少必须为 2.6 GHz 或更快。
* TPS-每秒事务数。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。
