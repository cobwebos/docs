---
title: 从存储分析度量值移动到 Azure Monitor 指标 |Microsoft Docs
description: 了解如何从存储分析度量值（经典指标）转换为 Azure Monitor 中的度量值。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 219d2b972089f9d3b7f84caa8b527474ac241c4f
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374161"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>转换到 Azure Monitor 中的指标

Azure 存储现在将指标集成到 Azure Monitor 平台。 **2023 年8月 31**日存储分析度量值，也称为*经典指标*。 如果使用经典指标，请确保在该日期之前过渡到 Azure Monitor 中的指标。 本文将帮助你进行转换。

## <a name="steps-to-complete-the-transition"></a>完成转换的步骤

若要过渡到 Azure Monitor 中的指标，建议采用以下方法。

1. 了解 Azure Monitor 中的典型指标和指标之间的一些[主要差异](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor)。 

2. 编译当前使用的经典度量值的列表。

3. 确定[Azure Monitor 中的哪些指标](#metrics-mapping-between-old-metrics-and-new-metrics)提供与当前使用的指标相同的数据。 
   
4. 创建用于查看指标数据的[图表](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal)或[仪表板](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal)。

   > [!NOTE]
   > 默认情况下会启用 Azure Monitor 中的度量值，因此，开始捕获指标时无需执行任何操作。 不过，您必须创建图表或仪表板来查看这些指标。 
 
5. 如果已创建基于经典存储指标的警报规则，则创建基于 Azure Monitor 中的指标的[警报规则](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。 

6. 在 Azure Monitor 中可以看到所有指标之后，可以关闭经典日志记录。 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>经典指标与 Azure Monitor 中的指标

本部分介绍这两个指标平台之间的几个重要区别。

主要区别在于指标的管理方式。 经典指标由 Azure 存储管理，而 Azure Monitor 中的指标由 Azure Monitor 管理。 借助经典指标，Azure 存储收集指标值，将其聚合，然后将其存储在存储帐户中的表中。 通过 Azure Monitor 中的指标，Azure 存储将指标数据发送到 Azure Monitor 后端。 Azure Monitor 提供了一种统一的监视体验，其中包括来自 Azure 门户的数据以及引入的数据。 

至于指标支持，经典指标仅提供适用于 Azure Blob 存储的**容量**指标。 Azure Monitor 中的指标提供 Blob、表、文件、队列和高级存储的容量指标。 经典指标提供 Blob、表、Azure 文件和队列存储的**事务**指标。 中的指标 Azure Monitor 向该列表中添加高级存储。

如果你的帐户中的活动未触发指标，则经典指标将为该指标显示值零（0）。 Azure Monitor 中的指标将完全省略数据，这会导致报告更加整洁。 例如，对于经典指标，如果未报告服务器超时错误，则 `ServerTimeoutError` 度量值表中的值将设置为0。 当查询的度量值的维度等于时，Azure Monitor 不返回任何数据 `Transactions` `ResponseType` `ServerTimeoutError` 。 

若要详细了解 Azure Monitor 中的指标，请参阅 [Azure Monitor 中的指标](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)。

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>将经典指标映射到 Azure Monitor 中的指标

 使用这些表来确定 Azure Monitor 中的哪些指标提供与当前使用的指标相同的数据。 

**容量指标**

| 经典指标 | Azure Monitor 中的指标 |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity`维度 `BlobType` 等于 `BlockBlob` 或`PageBlob` |
| `ObjectCount`        | `BlobCount`维度 `BlobType` 等于 `BlockBlob` 或`PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> 还有几个新的容量指标，不能用作经典指标。 若要查看完整列表，请参阅[度量值](../common/monitor-storage-reference.md#metrics)。

**事务指标**

| 经典指标 | Azure Monitor 中的指标 |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | 维度 `ResponseType` 等于 `AuthorizationError` ，维度 `Authentication` 等于的事务`Anonymous` |
| `AnonymousClientOtherError` | 维度 `ResponseType` 等于 `ClientOtherError` ，维度 `Authentication` 等于的事务`Anonymous` |
| `AnonymousClientTimeoutError` | 维度 `ResponseType` 等于 `ClientTimeoutError` ，维度 `Authentication` 等于的事务`Anonymous` |
| `AnonymousNetworkError` | 维度 `ResponseType` 等于 `NetworkError` ，维度 `Authentication` 等于的事务`Anonymous` |
| `AnonymousServerOtherError` | 维度 `ResponseType` 等于 `ServerOtherError` ，维度 `Authentication` 等于的事务`Anonymous` |
| `AnonymousServerTimeoutError` | 维度 `ResponseType` 等于 `ServerTimeoutError` ，维度 `Authentication` 等于的事务`Anonymous` |
| `AnonymousSuccess` | 维度 `ResponseType` 等于 `Success` ，维度 `Authentication` 等于的事务`Anonymous` |
| `AnonymousThrottlingError` | 维度 `ResponseType` 等于 `ClientThrottlingError` 或 `ServerBusyError` 且维度 `Authentication` 等于的事务`Anonymous` |
| `AuthorizationError` | 维度等于的事务 `ResponseType``AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | 维度等于的事务 `ResponseType``ClientOtherError` |
| `ClientTimeoutError` | 维度等于的事务 `ResponseType``ClientTimeoutError` |
| `NetworkError` | 维度等于的事务 `ResponseType``NetworkError` |
| `PercentAuthorizationError` | 维度等于的事务 `ResponseType``AuthorizationError` |
| `PercentClientOtherError` | 维度等于的事务 `ResponseType``ClientOtherError` |
| `PercentNetworkError` | 维度等于的事务 `ResponseType``NetworkError` |
| `PercentServerOtherError` | 维度等于的事务 `ResponseType``ServerOtherError` |
| `PercentSuccess` | 维度等于的事务 `ResponseType``Success` |
| `PercentThrottlingError` | 维度 `ResponseType` 等于或的事务 `ClientThrottlingError``ServerBusyError` |
| `PercentTimeoutError` | 维度 `ResponseType` 等于 `ServerTimeoutError` 或 `ResponseType` 等于的事务`ClientTimeoutError` |
| `SASAuthorizationError` | 维度 `ResponseType` 等于 `AuthorizationError` ，维度 `Authentication` 等于的事务`SAS` |
| `SASClientOtherError` | 维度 `ResponseType` 等于 `ClientOtherError` ，维度 `Authentication` 等于的事务`SAS` |
| `SASClientTimeoutError` | 维度 `ResponseType` 等于 `ClientTimeoutError` ，维度 `Authentication` 等于的事务`SAS` |
| `SASNetworkError` | 维度 `ResponseType` 等于 `NetworkError` ，维度 `Authentication` 等于的事务`SAS` |
| `SASServerOtherError` | 维度 `ResponseType` 等于 `ServerOtherError` ，维度 `Authentication` 等于的事务`SAS` |
| `SASServerTimeoutError` | 维度 `ResponseType` 等于 `ServerTimeoutError` ，维度 `Authentication` 等于的事务`SAS` |
| `SASSuccess` | 维度 `ResponseType` 等于 `Success` ，维度 `Authentication` 等于的事务`SAS` |
| `SASThrottlingError` | 维度 `ResponseType` 等于 `ClientThrottlingError` 或 `ServerBusyError` 且维度 `Authentication` 等于的事务`SAS` |
| `ServerOtherError` | 维度等于的事务 `ResponseType``ServerOtherError` |
| `ServerTimeoutError` | 维度等于的事务 `ResponseType``ServerTimeoutError` |
| `Success` | 维度等于的事务 `ResponseType``Success` |
| `ThrottlingError` | `Transactions`维度 `ResponseType` 等于 `ClientThrottlingError` 或`ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>后续步骤

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Azure Monitor 中的存储指标](./storage-metrics-in-azure-monitor.md)
