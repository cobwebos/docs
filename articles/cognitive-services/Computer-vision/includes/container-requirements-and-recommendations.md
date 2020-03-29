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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481755"
---
> [!NOTE]
> 要求和建议基于每秒一个请求的基准，使用包含 29 行和总共 803 个字符的扫描业务信函的 8 MB 图像。

下表描述了每个 Read 容器的最小和建议的资源分配。

| 容器 | 最小值 | 建议 |TPS<br>(最小值, 最大值)|
|-----------|---------|-------------|--|
| 读取 | 1 个内核，8 GB 内存，0.24 TPS | 8 个内核，16-GB 内存，1.17 TPS | 0.24, 1.17 |

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。
* TPS - 每秒事务。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。
