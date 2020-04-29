---
title: include 文件
description: include 文件
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81263331"
---
下面总结了正式版中的主要限制。

### <a name="sku-ingress-rates-and-capacities"></a>SKU 入口速率和容量

S1 和 S2 SKU 入口速率和容量可在配置新的时序见解环境时提供灵活性。 SKU 容量根据事件数或存储的字节数指示每日入口速率，以先达到的条件为准。 请注意，入口*每分钟*测量一次，并使用令牌桶算法来应用**限制**。 入口以 1 KB 的块计量。 例如，0.8 KB 的实际事件会衡量为一个事件，2.6 KB 事件会计为三个事件。

| S1 SKU 容量 | 入口速率 | 最大存储容量
| --- | --- | --- |
| 1 | 每天 1 GB （1000000个事件） | 每月 30 GB（3 千万个事件） |
| 10 | 每天 10 GB （10000000个事件） | 每月 300 GB（3 亿个事件） |

| S2 SKU 容量 | 入口速率 | 最大存储容量
| --- | --- | --- |
| 1 | 每天 10 GB （10000000个事件） | 每月 300 GB（3 亿个事件） |
| 10 | 每天 100 GB （100000000个事件） | 每月 3 TB（30 亿个事件） |

> [!NOTE]
> 容量呈线性增长，因此容量为 2 的 S1 SKU 每日入口速率支持 2 GB（2 百万）的事件，每月支持 60 GB（6 千万）的事件。

S2 SKU 环境每月支持更多的事件，并具有显著更高的入口容量。

| SKU  | 每月事件计数  | 每分钟事件计数 | 每分钟事件大小  |
|---------|---------|---------|---------|---------|
| S1     |   3000 万   |  720    |  720 KB   |
 |S2     |   3 亿   | 7,200   | 7,200 KB  |

### <a name="property-limits"></a>属性限制

GA 属性限制取决于所选的 SKU 环境。 提供的事件属性具有相应的 JSON、CSV 和图表列，可以在 [Azure 时序见解资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)中查看。

| SKU | 最大属性数 |
| --- | --- |
| S1 | 600 属性（列） |
| S2 | 800 属性（列） |

### <a name="event-sources"></a>事件源

每个实例最多支持两个事件源。 

* 了解如何[添加事件中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。
* 配置 [IoT 中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)。

### <a name="api-limits"></a>API 限制

[REST API 参考文档](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)中指定了时序见解正式版的 REST API 限制。