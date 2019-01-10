---
title: include 文件
description: include 文件
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/21/2018
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 6b9b40c457766573be0d72cb7f4ebe2328a070f5
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54026258"
---
每个应用程序（即每个检测密钥）的度量值和的事件数都具有一定限制。 限制取决于选择的[定价计划](https://azure.microsoft.com/pricing/details/application-insights/)。

| 资源 | 默认限制 | 注意
| --- | --- | --- |
| 每日的总数据量 | 100 GB | 可以通过设置一个上限来减少数据。 如果需要更多数据，可以在门户中最多将上限提高到 1,000 GB。 如需大于 1,000 GB 的容量，请将邮件发送到 AIDataCap@microsoft.com。
| 限制 | 32000 个事件/秒 | 限制按分钟计量。
| 数据保留 | 90 天 | 此资源适用于[搜索](../articles/azure-monitor/app/diagnostic-search.md)、[分析](../articles/azure-monitor/app/analytics.md)和[指标资源管理器](../articles/azure-monitor/app/metrics-explorer.md)。
| [可用性多步骤测试](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests)详细结果保留 | 90 天 | 此资源提供了每个步骤的详细结果。
| 最大事件大小 | 64 K | 
| 属性和指标名称长度 | 150 | 请参阅[类型架构](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)。
| 属性值字符串长度 | 8,192 | 请参阅[类型架构](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)。
| 跟踪和异常消息长度 | 10 K | 请参阅[类型架构](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)。
| 每个应用的[可用性测试](../articles/azure-monitor/app/monitor-web-app-availability.md)计数 | 100 |
| [探查器](../articles/azure-monitor/app/profiler.md)数据保留期 | 5 天 |
| 每天发送的[探查器](../articles/azure-monitor/app/profiler.md)数据量 | 10 GB |

有关详细信息，请参阅[关于 Application Insights 中的定价和配额](../articles/azure-monitor/app/pricing.md)。