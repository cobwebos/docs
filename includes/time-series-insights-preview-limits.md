---
title: include 文件
description: include 文件
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77123181"
---
### <a name="general-availability-and-preview-comparison"></a>公开上市和预览比较

下表总结了 Azure 时序见解正式发行版（GA）和预览版实例之间的几个重要区别。

| | GA | 预览 |
| --- | --- | ---|
| 第一类公民 | 以事件为中心 | 时间系列-中心 |
| 语义推理 | 低级别（引用数据） | 高级（模型） |
| 数据 contextualization | 非设备级别 | 设备和非设备级别 |
| 计算逻辑存储 | 是 | 存储在模型的类型变量中 |
| 存储和访问控制 | 是 | 已通过模型启用 |
| 聚合/采样 | 是 | 事件加权和时间加权 |
| 信号重建 | 是 | 评估 |
| 衍生时序的生产 | 是 | 是，合并和联接 |
| 语言灵活性 | 不可组合 | 组合 |
| 表达式语言 | 谓词字符串 | 时序表达式（谓词字符串、值、表达式和函数） |

### <a name="property-limits"></a>属性限制

在 GA 中，时序见解属性限制增加到1000，最大上限为800。 提供的事件属性具有相应的 JSON、CSV 和图表列，你可以在时序[见解预览资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)中查看这些列。

| SKU | 最大属性 |
| --- | --- |
| 预览 PAYG | 1000属性（列） |
| GA S1 | 600属性（列） |
| GA S2 | 800属性（列） |

### <a name="event-sources"></a>事件源

每个实例最多支持两个事件源。 

* 了解如何[添加事件中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。
* 配置[IoT 中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)。

默认情况下，[预览版环境支持](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations)**每秒 1 mb/秒（MB/s）每个环境**的入口速率。 如果需要，客户可以将其预览版环境扩展到**16 MB/秒**的吞吐量。 此外，每个分区的限制为**0.5 MB/s**。 

### <a name="api-limits"></a>API 限制

时序见解预览 REST API 限制在[REST API 参考文档](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)中指定。
