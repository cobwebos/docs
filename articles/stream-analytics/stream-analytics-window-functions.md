---
title: Azure 流分析开窗函数简介
description: 本文介绍在 Azure 流分析作业中使用的四个开窗函数（翻转、跳跃、滑动、会话）。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: 4c8d2143d2b6e18de2669a6b45961e601cc394bb
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707551"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>流分析开窗函数简介

在实时流方案中，对临时窗口中包含的数据执行操作是一种常见模式。 流分析提供对开窗函数的本机支持，使开发人员能够最小的工作量创建复杂的流进程作业。

有五种类型的时态窗口可供选择： [**翻转**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics)、 [**跳跃**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics)、 [**滑动**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)、 [**会话**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics)和 [**快照**](https://docs.microsoft.com/stream-analytics-query/snapshot-window-azure-stream-analytics) 窗口。  可在流分析作业中查询语法的 [GROUP BY](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) 子句中使用开窗函数  。 还可以使用 [Windows() 函数](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics)聚合在多个窗口中聚合事件。

所有[开窗](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics)操作都在窗口“结束”时输出结果  。 请注意，在启动流分析作业时，可以指定 *作业输出开始时间* ，系统将自动提取传入流中的以前事件，以在指定时间输出第一个窗口;例如，当你从 *Now* 选项开始时，它将立即开始发出数据。 窗口的输出是基于所用聚合函数的单个事件。 该输出事件包含窗口的结束时间戳，所有开窗函数都以固定的长度定义。 

![流分析开窗函数的概念](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>翻转窗口
翻转开窗函数用于将数据流划分成不同的时间段并对其执行某个函数，如以下示例所示。 翻转窗口的主要差异在于它们会重复，不重叠，并且一个事件不能属于多个翻转窗口。

![流分析翻转窗口](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>跳跃窗口
跳跃开窗函数在一段固定的时间内向前跳跃。 可以轻松地将它们视为翻转的窗口，这些窗口可以重叠，并且比窗口大小更频繁地发出。 事件可以属于多个跳跃窗口结果集。 要使跳跃窗口与翻转窗口相同，需将跃点大小指定为与窗口大小相同。 

![流分析跳跃窗口](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>滑动窗口

滑动窗口不同于翻转或跳跃窗口，仅在窗口内容实际更改的时间点输出事件。 换句话说，事件进入或退出窗口时。 每个窗口至少有一个事件，如跳跃窗口，事件可以属于多个滑动窗口

![流分析滑动窗口](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>会话窗口
会话窗口函数对差不多同时到达的事件进行分组，筛选出没有数据的时间段。 它具有三个主要参数：超时、最长持续时间和分区键（可选）。

![流分析会话窗口](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

第一个事件发生时，会话窗口开始。 如果在上一个引入事件后的指定超时期间内发生另一事件，那么窗口将扩展到包含该新事件。 反之，如果超时期间内没有发生事件，则窗口在超时时关闭。

如果指定超时期间内持续发生事件，则会话窗口将持续扩展，直到达到最长持续时间。 最长持续时间检间隔设置为与指定最长持续时间相同。 例如，如果最长持续时间为 10，则将在 t = 0、10、20、30 等时，检查窗口是否超出最长持续时间。

如果提供了分区键，则事件按该键组合在一起，会话窗口将分别应用于每个组。 在需要将不同会话窗口用于不同用户或设备时，此分区十分有帮助。

## <a name="snapshot-window"></a>快照窗口

快照窗口将具有相同时间戳的事件分组。 与其他需要特定窗口函数（如 [SessionWindow()](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics)）的窗口类型不同，可以通过将 System.Timestamp() 添加到 GROUP BY 子句来应用快照窗口。

![流分析快照窗口](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

