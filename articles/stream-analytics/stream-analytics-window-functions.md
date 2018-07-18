---
title: Azure 流分析开窗函数简介
description: 本文介绍在 Azure 流分析作业中使用的四个开窗函数（翻转、跳跃、滑动、会话）。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 2650058e277bc0338c779655ce381be046fb120a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33893637"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>流分析开窗函数简介
在实时流方案中，对临时窗口中包含的数据执行操作是一种常见模式。 流分析提供对开窗函数的本机支持，使开发人员能够最小的工作量创建复杂的流进程作业。

有四种类型临时窗口可供选择：[“翻转”](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics)、[“跳跃”](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics)、[“滑动”](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics)和[“会话”](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics)窗口。  可在流分析作业中查询语法的 [GROUP BY](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) 子句中使用开窗函数。

所有[开窗](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics)操作都在窗口“结束”时输出结果。 窗口的输出是基于所用聚合函数的单个事件。 该输出事件包含窗口的结束时间戳，所有开窗函数都以固定的长度定义。 

![流分析开窗函数的概念](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>翻转窗口
翻转开窗函数用于将数据流划分成不同的时间段并对其执行某个函数，如以下示例所示。 翻转窗口的主要差异在于它们会重复，不重叠，并且一个事件不能属于多个翻转窗口。

![流分析翻转窗口](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>跳跃窗口
跳跃开窗函数在一段固定的时间内向前跳跃。 人们往往将此类窗口视为可以重叠的翻转窗口，因此一个事件可以属于多个跳跃窗口结果集。 要使跳跃窗口与翻转窗口相同，需将跃点大小指定为与窗口大小相同。 

![流分析跳跃窗口](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>滑动窗口
不同于翻转或跳跃窗口，滑动开窗函数**只**在事件发生时生成输出。 每个窗口至少有一个事件，并且窗口持续根据 € (epsilon) 向前移动。 与跳跃窗口一样，事件可以属于多个滑动窗口。

![流分析滑动窗口](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window-preview"></a>会话窗口（预览版）
会话窗口函数对差不多同时到达的事件进行分组，筛选出没有数据的时间段。 它具有三个主要参数：超时、最长持续时间和分区键（可选）。

![流分析会话窗口](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

第一个事件发生时，会话窗口开始。 如果在上一个引入事件后的指定超时期间内发生另一事件，那么窗口将扩展到包含该新事件。 反之，如果超时期间内没有发生事件，则窗口在超时时关闭。

如果指定超时期间内持续发生事件，则会话窗口将持续扩展，直到达到最长持续时间。 最长持续时间检间隔设置为与指定最长持续时间相同。 例如，如果最长持续时间为 10，则将在 t = 0、10、20、30 等时，检查窗口是否超出最长持续时间。

如果提供了分区键，则事件按该键组合在一起，会话窗口将分别应用于每个组。 在需要将不同会话窗口用于不同用户或设备时，此分区十分有帮助。


## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

