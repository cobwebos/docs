---
title: include 文件
description: include 文件
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: c68e58bb18a5dba07855234af07b0a8be767bed0
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2020
ms.locfileid: "75928199"
---
每个应用程序（即每个检测密钥）的度量值和事件数有一些限制。 限制取决于选择的[定价计划](https://azure.microsoft.com/pricing/details/application-insights/)。

| 资源 | 默认限制 | 说明
| --- | --- | --- |
| 每日的总数据量 | 100 GB | 可以通过设置一个上限来减少数据。 如果需要更多数据，可以在门户中最多将上限提高到 1,000 GB。 对于大于 1000 GB 的容量，请将电子邮件发送到 AIDataCap@microsoft.com。
| 限制 | 每秒32000事件数 | 限制按分钟计量。
| 数据保留 | 90 天 | 此资源适用于[搜索](../articles/azure-monitor/app/diagnostic-search.md)、[分析](../articles/azure-monitor/app/analytics.md)和[指标资源管理器](../articles/azure-monitor/app/metrics-explorer.md)。
| [可用性多步骤测试](../articles/azure-monitor/app/availability-multistep.md)详细结果保留 | 90 天 | 此资源提供了每个步骤的详细结果。
| 最大事件大小 | 64000000字节 |
| 属性和指标名称长度 | 150 | 请参阅[类型架构](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/)。
| 属性值字符串长度 | 8,192 | 请参阅[类型架构](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/)。
| 跟踪和异常消息长度 | 32,768  | 请参阅[类型架构](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/)。
| 每个应用的[可用性测试](../articles/azure-monitor/app/monitor-web-app-availability.md)计数 | 100 |
| [探查器](../articles/azure-monitor/app/profiler.md)数据保留期 | 5 天 |
| 每天发送的[探查器](../articles/azure-monitor/app/profiler.md)数据量 | 10 GB |

有关详细信息，请参阅[关于 Application Insights 中的定价和配额](../articles/azure-monitor/app/pricing.md)。
