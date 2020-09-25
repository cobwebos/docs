---
title: include 文件
description: include 文件
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 7259e1981f873c8385a02fe4f353dcdda495f823
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287401"
---
### <a name="property-limits"></a>属性限制

在 Gen1 中，Azure 时序见解属性限制增加到1000，最大上限为800。 提供的事件属性具有相应的 JSON、CSV 和图表列，你可以在 [Azure 时序见解 Gen2 资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)中查看这些列。

| SKU | 最大属性数 |
| --- | --- |
| Gen2 (L1)  | 1,000 属性（列） |
| Gen1 (S1)  | 600 属性（列） |
| Gen1 (S2)  | 800 属性（列） |

### <a name="streaming-ingestion"></a>流式引入

* 每个环境最多只能有两个 [事件源](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) 。

* 可在[此处](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)找到有关事件源的最佳实践和一般指南

* 默认情况下，对于每个 Azure 时序见解第 2 代环境，Azure 时序见解第 2 代可按**每秒最多 1 兆字节 (MBps)** 的速率引入传入的数据。 存在针对[单个中心分区](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)的其他限制。 通过 Azure 门户提交支持票证可提供最多 8 MBps 的速率。 若要了解详细信息，请阅读 [流式引入吞吐量限制](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)。

### <a name="api-limits"></a>API 限制

[REST API 参考文档](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)中指定了 Azure 时序见解 Gen2 REST API 限制。
