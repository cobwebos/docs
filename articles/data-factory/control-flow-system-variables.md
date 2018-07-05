---
title: Azure 数据工厂中的系统变量 | Microsoft Docs
description: 本文介绍了 Azure 数据工厂支持的系统变量。 定义数据工厂实体时，可以在表达式中使用这些变量。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 43ea8703bdbfc23511c831a5f91c9461948cc254
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055339"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure 数据工厂支持的系统变量
本文介绍了 Azure 数据工厂支持的系统变量。 定义数据工厂实体时，可以在表达式中使用这些变量。

## <a name="pipeline-scope"></a>管道范围
可以在管道 JSON 中的任何位置引用这些系统变量。

| 变量名 | 说明 |
| --- | --- |
| @pipeline().DataFactory |在其中运行管道运行的数据工厂的名称 |
| @pipeline().Pipeline |管道名称 |
| @pipeline().RunId | 特定管道运行的 ID |
| @pipeline().TriggerType | 调用管道的触发器类型（手动、计划程序） |
| @pipeline().TriggerId| 调用管道的触发器 ID |
| @pipeline().TriggerName| 调用管道的触发器名称 |
| @pipeline().TriggerTime| 触发器调用管道的时间。 触发时间是指实际触发的时间而不是计划时间。 例如，返回 `13:20:08.0149599Z` 而不是 `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>计划触发器范围
如果触发器的类型为“ScheduleTrigger”，则可以在触发器 JSON 中的任何位置引用这些系统变量。

| 变量名 | 说明 |
| --- | --- |
| @trigger().scheduledTime |触发器计划调用管道运行的时间。 例如，对于每 5 分钟触发一次的触发器，此变量将分别返回 `2017-06-01T22:20:00Z`、`2017-06-01T22:25:00Z`、`2017-06-01T22:29:00Z`。|
| @trigger().startTime |触发器**实际**触发调用管道运行的时间。 例如，对于每 5 分钟触发一次的触发器，此变量可能分别返回类似此 `2017-06-01T22:20:00.4061448Z`、`2017-06-01T22:25:00.7958577Z`、`2017-06-01T22:29:00.9935483Z` 的内容。|

## <a name="tumbling-window-trigger-scope"></a>翻转窗口触发器范围
如果触发器的类型为“TumblingWindowTrigger”，则可以在触发器 JSON 中的任何位置引用这些系统变量。

| 变量名 | 说明 |
| --- | --- |
| @trigger().outputs.windowStartTime |在触发器计划调用管道运行时启动窗口。 如果翻转窗口触发器的频率为“每小时”，则这将是小时开始时的时间。|
| @trigger().outputs.windowEndTime |在触发器计划调用管道运行时结束窗口。 如果翻转窗口触发器的频率为“每小时”，则这将是小时结束时的时间。|
## <a name="next-steps"></a>后续步骤
有关这些变量如何用于表达式的信息，请参阅[表达式语言和函数](control-flow-expression-language-functions.md)。
