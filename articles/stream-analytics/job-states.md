---
title: Azure 流分析作业状态
description: 本文介绍流分析作业的不同状态
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/06/2019
ms.openlocfilehash: 28e0e69d3a6a4d3a38146cbf2c49426b3b16c784
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789444"
---
# <a name="azure-stream-analytics-job-states"></a>Azure 流分析作业状态

在任何给定时间，流分析作业可以是四种状态之一。 可以在 Azure 门户的流分析作业的“概览”页上找到作业的状态。 

| 状态 | 描述 | 建议的操作 |
| --- | --- | --- |
| **正在运行** | 作业在来自已定义输入源的 Azure 读取事件上运行，对其进行处理并将结果写入配置的输出接收器。 | 最佳做法是通过监视[关键指标](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)来跟踪作业的性能。 |
| **已停止** | 作业已停止，不处理事件。 | NA | 
| **已降级** | 暂时性错误可能会影响作业。 流分析会立即尝试从此类错误恢复，然后回到“正在运行”状态（在数分钟内）。 之所以发生这些错误，可能是因为网络问题、其他 Azure 资源的可用性问题、反序列化错误，等等。当作业处于降级状态时，其性能可能受影响。| 可以查看[诊断或活动日志](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs)，详细了解这些暂时性错误的原因。 如果发生此类反序列化错误，建议采取纠正措施，确保不会导致事件异常。 如果作业总是达到资源使用率限制，可尝试提高 SU 数或[将作业并行化](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)。 在其他情况下，如果你无法采取任何措施，流分析会尝试恢复到“正在运行”状态。   |
| 失败  | 作业遇到严重错误，导致状态变为“失败”。 事件无法读取和处理。 作业最终处于失败状态的常见原因是运行时错误。 | 可以[配置警报](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal)，这样当作业进入“失败”状态时，系统会通知你。 <br> <br>可以使用[活动和诊断日志](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs)进行调试，以便确定根本原因并解决问题。|

## <a name="next-steps"></a>后续步骤
* [为 Azure 流分析作业设置警报](stream-analytics-set-up-alerts.md)
* [可以在流分析中使用的指标](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [使用活动和诊断日志进行故障排除](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
