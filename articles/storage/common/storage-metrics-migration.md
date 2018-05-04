---
title: Azure 存储指标迁移 | Microsoft Docs
description: 了解如何将旧指标迁移到由 Azure Monitor 托管的新指标。
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: c4dc9b231668315af16c625314c737fee99d672d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-metrics-migration"></a>Azure 存储指标迁移

按照 Azure 中统一监视体验的策略，Azure 存储会将指标载入 Azure Monitor 平台。 将来，终止旧指标服务前将基于 Azure 策略进行提前通知。 如果使用旧存储指标，则需要在服务终止日期前进行迁移，以便保留指标信息。

本文说明如何将旧指标迁移到新指标。

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>了解 Azure 存储托管的旧指标

Azure 存储收集旧指标值，将其聚合并存储在同一存储帐户内的 $Metric 表中。 可以使用 Azure 门户设置监视图表，还可以基于架构使用 Azure 存储 SDK 读取 $Metric 表中的数据。 有关详细信息，请参阅[存储分析](./storage-analytics.md)。

旧指标提供容量指标（仅限 Blob 上）和 Blob、表、文件和队列上的事务指标。

旧指标采用平面架构设计。 当没有可触发指标的流量模式时，此设计会导致生成指标值 0。 例如，即使未在流向存储帐户的实时流量中收到任何服务器超时错误，$Metric 表中的 ServerTimeoutError 也将设置为 0。

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>了解 Azure Monitor 托管的新指标

对于新存储指标，Azure 存储会向 Azure Monitor 后端发出指标数据。 Azure Monitor 对通过门户和数据引入获取的数据提供统一的监视体验。 有关详细信息，请参阅此[文章](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

新指标提供 Blob、表、文件、队列和高级存储上的容量指标和事务指标。

多维是 Azure Monitor 提供的功能之一。 Azure 存储采用这一设计来定义新指标架构。 有关指标支持的维度的详细信息，请参阅 [Azure Monitor 中的 Azure 存储指标](./storage-metrics-in-azure-monitor.md)。 多维设计可节约存储指标的带宽（包括引入和容量两方面）成本。 因此，如果流量未触发相关指标，则不会生成相关指标数据。 例如，如果流量未触发任何服务器超时错误，当查询事务指标（维度 ResponseType 等于 ServerTimeoutError）的值时，Azure Monitor 不会返回任何数据。

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>旧指标和新指标之间的指标映射

如果以编程方式读取指标数据，则需要在程序中采用新指标架构。 为了更好地了解相关更改，可以参考下表中列出的映射：

**容量指标**

| 旧指标 | 新指标 |
| ------------------- | ----------------- |
| Capacity            | BlobCapacity（维度 BlobType 等于 BlockBlob 或 PageBlob） |
| ObjectCount         | BlobCount（维度 BlobType 等于 BlockBlob 或 PageBlob） |
| ContainerCount      | ContainerCount |

以下是旧指标不支持的新指标：
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**事务指标**

| 旧指标 | 新指标 |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | 事务（维度 ResponseType 等于 AuthorizationError） |
| AnonymousClientOtherError | 事务（维度 ResponseType 等于 ClientOtherError） |
| AnonymousClientTimeoutError | 事务（维度 ResponseType 等于 ClientTimeoutError） |
| AnonymousNetworkError | 事务（维度 ResponseType 等于 NetworkError） |
| AnonymousServerOtherError | 事务（维度 ResponseType 等于 ServerOtherError） |
| AnonymousServerTimeoutError | 事务（维度 ResponseType 等于 ServerTimeoutError） |
| AnonymousSuccess | 事务（维度 ResponseType 等于 Success） |
| AnonymousThrottlingError | 事务（维度 ResponseType 等于 ClientThrottlingError 或 ServerBusyError） |
| AuthorizationError | 事务（维度 ResponseType 等于 AuthorizationError） |
| 可用性 | 可用性 |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | 事务（维度 ResponseType 等于 ClientOtherError） |
| ClientTimeoutError | 事务（维度 ResponseType 等于 ClientTimeoutError） |
| NetworkError | 事务（维度 ResponseType 等于 NetworkError） |
| PercentAuthorizationError | 事务（维度 ResponseType 等于 AuthorizationError） |
| PercentClientOtherError | 事务（维度 ResponseType 等于 ClientOtherError） |
| PercentNetworkError | 事务（维度 ResponseType 等于 NetworkError） |
| PercentServerOtherError | 事务（维度 ResponseType 等于 ServerOtherError） |
| PercentSuccess | 事务（维度 ResponseType 等于 Success） |
| PercentThrottlingError | 事务（维度 ResponseType 等于 ClientThrottlingError 或 ServerBusyError） |
| PercentTimeoutError | 事务（维度 ResponseType 等于 ServerTimeoutError 或 ResponseType 等于 ClientTimeoutError）|
| SASAuthorizationError | 事务（维度 ResponseType 等于 AuthorizationError） |
| SASClientOtherError | 事务（维度 ResponseType 等于 ClientOtherError） |
| SASClientTimeoutError | 事务（维度 ResponseType 等于 ClientTimeoutError） |
| SASNetworkError | 事务（维度 ResponseType 等于 NetworkError） |
| SASServerOtherError | 事务（维度 ResponseType 等于 ServerOtherError） |
| SASServerTimeoutError | 事务（维度 ResponseType 等于 ServerTimeoutError） |
| SASSuccess | 事务（维度 ResponseType 等于 Success） |
| SASThrottlingError | 事务（维度 ResponseType 等于 ClientThrottlingError 或 ServerBusyError） |
| ServerOtherError | 事务（维度 ResponseType 等于 ServerOtherError） |
| ServerTimeoutError | 事务（维度 ResponseType 等于 ServerTimeoutError） |
| 成功 | 事务（维度 ResponseType 等于 Success） |
| ThrottlingError | 事务（维度 ResponseType 等于 ClientThrottlingError 或 ServerBusyError） |
| TotalBillableRequests | 事务 |
| TotalEgress | 流出量 |
| TotalIngress | 流入量 |
| TotalRequests | 事务 |

## <a name="faq"></a>常见问题

* 应如何迁移现有警报规则？

答：如果基于旧存储指标创建了经典警报规则，则需要基于新指标架构创建新警报规则。

* 新指标数据是否会默认存储在同一存储帐户中？

答：没有。 如果需要将指标数据存档到存储帐户，可以使用 [Azure Monitor 中的诊断设置](https://azure.microsoft.com/blog/azure-monitor-multiple-diagnostic-settings/)

## <a name="next-steps"></a>后续步骤

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Azure Monitor 中的存储指标](./storage-metrics-in-azure-monitor.md)
