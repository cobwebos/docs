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
ms.openlocfilehash: 3ab5462cc2b368bd1518606b03c8ec1027bc31c3
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397176"
---
> [!NOTE]
> 这些要求和建议基于这样的基准：每秒一个请求，使用包含 29 行和总共 803 个字符的经过扫描的业务信函的 8 MB。

下表显示了每个读取容器的最小和建议的资源分配。

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 阅读 2.0-预览 | 1核，8 GB 内存 |  8核，16 GB 内存 |
| 阅读 3.0-预览 | 8核，16 GB 内存 | 8核，24 GB 内存 |
| 阅读 3.1-预览 | 8核，16 GB 内存 | 8核，24 GB 内存 |

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。
