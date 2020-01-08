---
title: Azure 流分析作业状态
description: 本文介绍了流分析作业的四种不同状态;正在运行、已停止、已降级和失败。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 454474333cac94dc25deae8196e9ba45bcb3a574
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359760"
---
# <a name="azure-stream-analytics-job-states"></a>Azure 流分析作业状态

流分析作业可在任意给定时间处于四种状态之一： "正在运行"、"已停止"、"已降级" 或 "失败"。 可以在 Azure 门户的流分析作业的“概览”页上找到作业的状态。 

| 状况 | Description | 建议的操作 |
| --- | --- | --- |
| **正在运行** | 作业在来自已定义输入源的 Azure 读取事件上运行，对其进行处理并将结果写入配置的输出接收器。 | 最佳做法是通过监视[关键指标](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)来跟踪作业的性能。 |
| **已停止** | 作业已停止，不处理事件。 | 不可用 | 
| **已降级** | 输入和输出连接可能存在间歇问题。 这些错误称为暂时性错误，这可能会使你的作业进入降级状态。 流分析会立即尝试从此类错误恢复，然后回到“正在运行”状态（在数分钟内）。 这些错误可能是由于网络问题、其他 Azure 资源的可用性、反序列化错误等引起的。当作业处于降级状态时，作业的性能可能会受到影响。| 可以查看[诊断或活动日志](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs)，详细了解这些暂时性错误的原因。 如果发生此类反序列化错误，建议采取纠正措施，确保不会导致事件异常。 如果作业总是达到资源使用率限制，可尝试提高 SU 数或[将作业并行化](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)。 在其他情况下，如果你无法采取任何措施，流分析会尝试恢复到“正在运行”状态。 <br> 可以使用[水位线延迟](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)指标来了解这些暂时性错误是否会影响作业的性能。|
| 失败 | 作业遇到严重错误，导致状态变为“失败”。 事件无法读取和处理。 作业最终处于失败状态的常见原因是运行时错误。 | 可以[配置警报](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal)，这样当作业进入“失败”状态时，系统会通知你。 <br> <br>可以使用[活动和诊断日志](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs)进行调试，以便确定根本原因并解决问题。|

## <a name="next-steps"></a>后续步骤
* [为 Azure 流分析作业设置警报](stream-analytics-set-up-alerts.md)
* [可以在流分析中使用的指标](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [使用活动和诊断日志进行故障排除](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
