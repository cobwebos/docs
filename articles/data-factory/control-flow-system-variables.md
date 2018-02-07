---
title: "Azure 数据工厂中的系统变量 | Microsoft Docs"
description: "本文介绍了 Azure 数据工厂支持的系统变量。 定义数据工厂实体时，可以在表达式中使用这些变量。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: bdf1754226852145e9bf5597256339549f253071
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure 数据工厂支持的系统变量
本文介绍了 Azure 数据工厂支持的系统变量。 定义数据工厂实体时，可以在表达式中使用这些变量。 

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 中的函数和变量](v1/data-factory-functions-variables.md)。


## <a name="pipeline-scope"></a>管道范围：

| 变量名 | 说明 |
| --- | --- |
| @pipeline().DataFactory |在其中运行管道运行的数据工厂的名称 | 
| @pipeline().Pipeline |管道名称 |
| @pipeline().RunId | 特定管道运行的 ID | 
| @pipeline().TriggerType | 调用管道的触发器类型（手动、计划程序） | 
| @pipeline().TriggerId| 调用管道的触发器 ID |
| @pipeline().TriggerName| 调用管道的触发器名称 |
| @pipeline().TriggerTime| 触发器调用管道的时间。 触发时间是指实际触发的时间而不是计划时间。 例如，返回 `13:20:08.0149599Z` 而不是 `13:20:00.00Z` |

## <a name="trigger-scope"></a>触发范围：

| 变量名 | 说明 |
| --- | --- |
| trigger().scheduledTime |触发器计划调用管道运行的时间。 例如，对于每 5 分钟触发一次的触发器，此变量将分别返回 `2017-06-01T22:20:00Z`、`2017-06-01T22:25:00Z`、`2017-06-01T22:29:00Z`。|
| trigger().startTime |触发器**实际**触发调用管道运行的时间。 例如，对于每 5 分钟触发一次的触发器，此变量可能分别返回类似此 `2017-06-01T22:20:00.4061448Z`、`2017-06-01T22:25:00.7958577Z`、`2017-06-01T22:29:00.9935483Z` 的内容。|

## <a name="next-steps"></a>后续步骤
有关这些变量如何用于表达式的信息，请参阅[表达式语言和函数](control-flow-expression-language-functions.md)。 
