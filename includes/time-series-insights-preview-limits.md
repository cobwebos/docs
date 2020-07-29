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
ms.openlocfilehash: 48080bb4d1e24f7f98d3dfe1fd63b65ba46df35e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289897"
---
### <a name="property-limits"></a>属性限制

在 Gen1 中，Azure 时序见解属性限制增加到1000，最大上限为800。 提供的事件属性具有相应的 JSON、CSV 和图表列，你可以在[Azure 时序见解 Gen2 资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)中查看这些列。

| SKU | 最大属性数 |
| --- | --- |
| Gen2 （L1） | 1,000 属性（列） |
| Gen1 （S1） | 600 属性（列） |
| Gen1 （S2） | 800 属性（列） |

### <a name="event-sources"></a>事件源

每个实例最多支持两个事件源。

* 了解如何[添加事件中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。
* 配置 [IoT 中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)。

默认情况下， [Gen2 环境支持](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingress-throughput-limits)**每秒 1 mb/秒（MB/s）每个环境**的入口速率。 如果需要，客户可以将其环境扩展到最高**16 MB/秒**的吞吐量。 还存在每个分区 0.5 MB/秒  的限制。

### <a name="api-limits"></a>API 限制

[REST API 参考文档](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)中指定了 Azure 时序见解 Gen2 REST API 限制。
