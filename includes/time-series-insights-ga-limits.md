---
title: include 文件
description: include 文件
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/16/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: e8c040bc186932680ce7eb4418a787dc061d02ce
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991147"
---
下面汇总了公开上市的主要限制。

### <a name="sku-ingress-rates-and-capacities"></a>SKU 入口速率和容量

S1 和 S2 SKU 入口速率和容量可在配置新的时序见解环境时提供灵活性。

| S1 SKU 容量 | 入口速率 | 最大存储容量
| --- | --- | --- |
| 第 | 1 GB（1 百万个事件） | 每月 30 GB（3 千万个事件） |
| 10 | 10 GB（1 千万个事件） | 每月 300 GB（3 亿个事件） |

| S2 SKU 容量 | 入口速率 | 最大存储容量
| --- | --- | --- |
| 第 | 10 GB（1 千万个事件） | 每月 300 GB（3 亿个事件） |
| 10 | 100 GB（1 亿个事件） | 每月 3 TB（30 亿个事件） |

> [!NOTE]
> 容量呈线性增长，因此容量为 2 的 S1 SKU 每日入口速率支持 2 GB（2 百万）的事件，每月支持 60 GB（6 千万）的事件。

S2 SKU 环境支持每个月更多的事件，并且入口容量大幅提高。

| SKU  | 每月事件计数  | 每月的事件大小  | 每分钟事件计数 | 每分钟事件大小  |
|---------|---------|---------|---------|---------|
| S1     |   3000 万     |  30GB     |  720    |  720 KB   |
 |S2     |   3 亿    |   300 GB   | 7,200   | 7,200 KB  |

### <a name="property-limits"></a>属性限制

GA 属性限制取决于所选的 SKU 环境。 提供的事件属性具有可在[时序见解资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)中查看的相应 JSON、CSV 和图表列。

| SKU | 最大属性 |
| --- | --- |
| S1 | 600属性（列） |
| S2 | 800属性（列） |

### <a name="event-sources"></a>事件源

每个实例最多支持两个事件源。 

* 了解如何[添加事件中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。
* 配置[IoT 中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)。

### <a name="api-limits"></a>API 限制

[REST API 参考文档](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)中指定了时序见解公开上市 REST API 限制。