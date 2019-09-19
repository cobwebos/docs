---
title: 容器要求和建议
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 47c889a428ec2916dc8cad73e30ef5ff630372a7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129816"
---
> [!NOTE]
> 要求和建议基于每秒单个请求的基准，使用 8 MB 的扫描业务信函图像，其中包含29行，总共803个字符。

#### <a name="readtabread"></a>[读取](#tab/read)

下表描述了每个读取容器的最小和建议的资源分配。

| 容器 | 最低要求 | 建议 |TPS<br>(最小值, 最大值)|
|-----------|---------|-------------|--|
| 读取 | 1个内核，8 GB 内存，0.24 TPS | 8核，16 GB 内存，1.17 TPS | 0.24、1.17 |

#### <a name="recognize-texttabrecognize-text"></a>[识别文本](#tab/recognize-text)

下表描述了每个识别文本容器的最小和建议分配的资源。

| 容器 | 最低要求 | 建议 |TPS<br>(最小值, 最大值)|
|-----------|---------|-------------|--|
| 识别文本 | 1核，8 GB 内存，0.12 TPS | 8核，16 GB 内存，0.60 TPS | 0.12、0.60 |

***

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。
* TPS-每秒事务数。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。
