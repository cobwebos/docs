---
title: 容器要求和建议
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877962"
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
