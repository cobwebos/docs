---
title: 从存储分析指标移动到 Azure Monitor 指标 | Microsoft Docs
description: 了解如何从存储分析指标（经典指标）转换到 Azure Monitor 中的指标。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: c6a5f69a5a32ed1279b367c93b5246eb77ef0208
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802831"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>转换到 Azure Monitor 中的指标

**2023 年8月 31**日存储分析度量值，也称为*经典指标*。 有关详细信息，请参阅 [官方公告](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/)。 如果使用经典指标，请确保在该日期之前过渡到 Azure Monitor 中的指标。 本文可帮助你进行转换。 

## <a name="steps-to-complete-the-transition"></a>完成转换的步骤

若要转换到 Azure Monitor 中的指标，建议采用以下方法。

1. 了解经典指标和 Azure Monitor 中的指标之间的一些[关键差别](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor)。 

2. 编译当前使用的经典指标列表。

3. 确定 [Azure Monitor 中的哪些指标](#metrics-mapping-between-old-metrics-and-new-metrics)提供与当前使用指标相同的数据。 
   
4. 创建[图标](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal)或[仪表板](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal)以查看指标数据。

   > [!NOTE]
   > 默认情况下，Azure Monitor 中的指标处于启用状态，因此无需执行任何操作来开始捕获指标。 但是，你必须创建图表或仪表板才能查看这些指标。 
 
5. 如果已创建基于经典存储指标的警报规则，则会根据 Azure Monitor 中的指标[创建警报规则](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。 

6. 在 Azure Monitor 中查看所有指标后，可以关闭经典日志记录。 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>经典指标与 Azure Monitor 中的指标

本部分介绍这两个指标平台之间的一些关键差别。

主要差别在于如何管理指标。 经典指标由 Azure 存储管理，而 Azure Monitor 中的指标由 Azure Monitor 管理。 使用经典指标，Azure 存储会收集并聚合指标值，然后将它们存储到位于存储帐户中的表中。 通过 Azure Monitor 中的指标，Azure 存储发送指标数据到 Azure Monitor 后端。 Azure Monitor 对通过 Azure 门户和引入的数据获取的数据提供统一的监视体验。 

经典指标会发送并存储在 Azure 存储帐户中。 Azure Monitor 度量值可以发送到多个位置。 一个存储帐户可以是这些位置中的一个，但不是必需的。  

至于指标支持，经典指标仅为 Azure Blob 存储提供容量指标。 Azure Monitor 中的指标提供 Blob、表、文件、队列和高级存储的容量指标。 经典指标提供 Blob、表、Azure 文件和队列存储上的事务指标。 Azure Monitor 中的指标向该列表添加高级存储。

如果帐户中的活动未触发指标，则经典指标将显示该指标的值为零 (0)。 Azure Monitor 中的指标将完全省略数据，让报告更简洁。 例如，对于经典指标，如果未报告服务器超时错误，则度量值表中的 `ServerTimeoutError` 值将设置为 0。 当你查询维度 `ResponseType` 等于 `ServerTimeoutError` 的指标 `Transactions` 的值时，Azure Monitor 不会返回任何数据。 

若要详细了解 Azure Monitor 中的指标，请参阅 [Azure Monitor 中的指标](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)。

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>将经典指标映射到 Azure Monitor 中的指标

 使用这些表可以标识 Azure Monitor 中哪些指标提供与当前使用的指标相同的数据。 

**容量指标**

| 经典指标 | Azure Monitor 中的指标 |
| ------------------- | ----------------- |
| `Capacity`            | 维度 `BlobType` 等于 `BlockBlob` 或 `PageBlob` 的 `BlobCapacity` |
| `ObjectCount`        | 维度 `BlobType` 等于 `BlockBlob` 或 `PageBlob` 的 `BlobCount` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> 还有一些新的容量指标不能作为经典指标提供。 若要查看完整列表，请参阅[指标](../common/monitor-storage-reference.md#metrics)。

**事务指标**

| 经典指标 | Azure Monitor 中的指标 |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | 维度 `ResponseType` 等于 `AuthorizationError` 且维度 `Authentication` 等于 `Anonymous` 的事务 |
| `AnonymousClientOtherError` | 维度 `ResponseType` 等于 `ClientOtherError` 且维度 `Authentication` 等于 `Anonymous` 的事务 |
| `AnonymousClientTimeoutError` | 维度 `ResponseType` 等于 `ClientTimeoutError` 且维度 `Authentication` 等于 `Anonymous` 的事务 |
| `AnonymousNetworkError` | 维度 `ResponseType` 等于 `NetworkError` 且维度 `Authentication` 等于 `Anonymous` 的事务 |
| `AnonymousServerOtherError` | 维度 `ResponseType` 等于 `ServerOtherError` 且维度 `Authentication` 等于 `Anonymous` 的事务 |
| `AnonymousServerTimeoutError` | 维度 `ResponseType` 等于 `ServerTimeoutError` 且维度 `Authentication` 等于 `Anonymous` 的事务 |
| `AnonymousSuccess` | 维度 `ResponseType` 等于 `Success` 且维度 `Authentication` 等于 `Anonymous` 的事务 |
| `AnonymousThrottlingError` | 维度 `ResponseType` 等于 `ClientThrottlingError` 或 `ServerBusyError` 且维度 `Authentication` 等于 `Anonymous` 的事务 |
| `AuthorizationError` | 维度 `ResponseType` 等于 `AuthorizationError` 的事务 |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | 维度 `ResponseType` 等于 `ClientOtherError` 的事务 |
| `ClientTimeoutError` | 维度 `ResponseType` 等于 `ClientTimeoutError` 的事务 |
| `NetworkError` | 维度 `ResponseType` 等于 `NetworkError` 的事务 |
| `PercentAuthorizationError` | 维度 `ResponseType` 等于 `AuthorizationError` 的事务 |
| `PercentClientOtherError` | 维度 `ResponseType` 等于 `ClientOtherError` 的事务 |
| `PercentNetworkError` | 维度 `ResponseType` 等于 `NetworkError` 的事务 |
| `PercentServerOtherError` | 维度 `ResponseType` 等于 `ServerOtherError` 的事务 |
| `PercentSuccess` | 维度 `ResponseType` 等于 `Success` 的事务 |
| `PercentThrottlingError` | 维度 `ResponseType` 等于 `ClientThrottlingError` 或 `ServerBusyError` 的事务 |
| `PercentTimeoutError` | 维度 `ResponseType` 等于 `ServerTimeoutError` 或维度 `ResponseType` 等于 `ClientTimeoutError` 的事务 |
| `SASAuthorizationError` | 维度 `ResponseType` 等于 `AuthorizationError` 且维度 `Authentication` 等于 `SAS` 的事务 |
| `SASClientOtherError` | 维度 `ResponseType` 等于 `ClientOtherError` 且维度 `Authentication` 等于 `SAS` 的事务 |
| `SASClientTimeoutError` | 维度 `ResponseType` 等于 `ClientTimeoutError` 且维度 `Authentication` 等于 `SAS` 的事务 |
| `SASNetworkError` | 维度 `ResponseType` 等于 `NetworkError` 且维度 `Authentication` 等于 `SAS` 的事务 |
| `SASServerOtherError` | 维度 `ResponseType` 等于 `ServerOtherError` 且维度 `Authentication` 等于 `SAS` 的事务 |
| `SASServerTimeoutError` | 维度 `ResponseType` 等于 `ServerTimeoutError` 且维度 `Authentication` 等于 `SAS` 的事务 |
| `SASSuccess` | 维度 `ResponseType` 等于 `Success` 且维度 `Authentication` 等于 `SAS` 的事务 |
| `SASThrottlingError` | 维度 `ResponseType` 等于 `ClientThrottlingError` 或 `ServerBusyError` 且维度 `Authentication` 等于 `SAS` 的事务 |
| `ServerOtherError` | 维度 `ResponseType` 等于 `ServerOtherError` 的事务 |
| `ServerTimeoutError` | 维度 `ResponseType` 等于 `ServerTimeoutError` 的事务 |
| `Success` | 维度 `ResponseType` 等于 `Success` 的事务 |
| `ThrottlingError` | 维度 `ResponseType` 等于 `ClientThrottlingError` 或 `ServerBusyError` 的 `Transactions`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>后续步骤

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)

