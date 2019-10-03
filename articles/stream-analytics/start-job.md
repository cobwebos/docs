---
title: 如何启动 Azure 流分析作业
description: 本文介绍如何从 Azure 门户、PowerShell 和 Visual Studio 启动流分析作业。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 1e4cb63accf7e89ac02451e9c25b9902a8a10812
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173273"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>如何启动 Azure 流分析作业

可以使用 Azure 门户、Visual Studio 和 PowerShell 启动 Azure 流分析作业。 启动作业时，可以选择该作业开始创建输出的时间。 Azure 门户、Visual Studio 和 PowerShell 各自提供不同的方法来设置开始时间。 下面将介绍这些方法。

## <a name="start-options"></a>启动选项
可使用以下三个选项来启动作业。 请注意，下面提到的所有时间都是在 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) 中指定的时间。 如果未指定 TIMESTAMP BY，将使用抵达时间。
* **立即**：使输出事件流的起点与作业启动时间相同。 如果使用时态运算符（例如时间窗口、LAG 或 JOIN），则 Azure 流分析会自动回查输入源中的数据。 例如，如果你“立即”启动一个作业，而查询使用 5 分钟翻转窗口，则 Azure 流分析将在输入中查找 5 分钟以前的数据。
第一个可能的输出事件具有等于或大于当前时间的时间戳，ASA 保证考虑到在逻辑上可对输出产生作用的所有输入事件。 例如，不会生成部分开窗聚合。 始终生成完整的聚合值。

* **自定义**：可以选择输出的起点。 与“立即”选项类似，如果使用时态运算符，Azure 流分析将自动读取此时间以前的数据 

* **上次停止时** 如果作业先前已启动，但被手动停止或失败，则可以使用此选项。 选择此选项时，Azure 流分析将使用上次输出时间来重启作业，因此不会丢失数据。 与前面的选项类似，如果使用时态运算符，Azure 流分析将自动读取此时间以前的数据。 由于多个输入分区可能具有不同的时间，而系统会使用所有分区的最早停止时间，因此，输出中可能会出现一些重复项。 [事件传送保证](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)页上提供了有关“正好处理一次”的详细信息。


## <a name="azure-portal"></a>Azure 门户

在 Azure 门户中导航到你的作业，然后在概述页上选择“启动”。 依次选择“作业输出开始时间”、“启动”。

选择“作业输出开始时间”对应的选项之一。 选项包括“立即”、“自定义”，如果作业先前已运行过，则还包括“上次停止时”。 有关这些选项的详细信息，请参阅上文。

## <a name="visual-studio"></a>Visual Studio

在作业视图中，选择绿色箭头按钮启动作业。 设置“作业输出启动模式”并选择“启动”。 作业状态将更改为“正在运行”。

“作业输出启动模式”有三个选项：*JobStartTime*、*CustomTime* 和 *LastOutputEventTime*。 如果此属性不存在，则默认值为 *JobStartTime*。 有关这些选项的详细信息，请参阅上文。


## <a name="powershell"></a>PowerShell

在 PowerShell 中使用以下 cmdlet 启动作业：

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

**OutputStartMode** 有三个选项：*JobStartTime*、*CustomTime* 和 *LastOutputEventTime*。 如果此属性不存在，则默认值为 *JobStartTime*。 有关这些选项的详细信息，请参阅上文。

有关 `Start-AzStreamAnalyitcsJob` cmdlet 的详细信息，请查看 [Start-AzStreamAnalyticsJob 参考](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [快速入门：使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)
* [快速入门：使用用于 Visual Studio 的 Azure 流分析工具创建流分析作业](stream-analytics-quick-create-vs.md)
