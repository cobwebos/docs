---
title: 如何启动 Azure 流分析作业
description: 本文介绍如何从 Azure 门户、PowerShell 和 Visual Studio 启动流分析作业。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426465"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>如何启动 Azure 流分析作业

可以使用 Azure 门户、Visual Studio 和 PowerShell 启动 Azure 流分析作业。 启动作业时，可以为作业选择开始创建输出的时间。 Azure 门户、Visual Studio 和 PowerShell 都有不同的方法来设置开始时间。 这些方法如下所述。

## <a name="start-options"></a>“开始”菜单选项
以下三个选项可用于启动作业。 请注意，下面提到的所有时间都是在[TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)中指定的时间。 如果未指定 TIMESTAMP BY，将使用到达时间。
* **现在**：使输出事件流的起始点与作业启动时相同。 如果使用时态运算符（例如，时间窗口、延迟或联接），则 Azure 流分析会自动查看输入源中的数据。 例如，如果你启动一个作业 "Now" 并且查询使用5分钟翻转窗口，Azure 流分析将在5分钟前从输入中查找数据。
第一个可能的输出事件应具有等于或大于当前时间的时间戳，ASA 保证对输出产生逻辑贡献的所有输入事件进行了考虑。 例如，不生成部分窗口的聚合。 它始终是完整的聚合值。

* **自定义**：可以选择输出的起点。 与**Now**选项类似，如果使用时态运算符，Azure 流分析将在此时间之前自动读取数据 

* **上次停止的时间**。 当作业以前已启动，但手动停止或失败时，此选项可用。 选择此选项时，Azure 流分析将使用上次输出时间来重新启动作业，因此不会丢失任何数据。 与先前的选项类似，如果使用时态运算符，Azure 流分析将在此时间之前自动读取数据。 由于几个输入分区可能具有不同的时间，因此，将使用所有分区的最早停止时间，因此可能会在输出中出现一些重复项。 有关一次性处理的详细信息，请访问页面[事件传送保证](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)。


## <a name="azure-portal"></a>Azure 门户

导航到 Azure 门户中的作业，然后在 "概述" 页上选择 "**启动**"。 选择**作业输出开始时间**，然后选择 "**启动**"。

选择 "**作业输出开始时间**" 选项之一。 选项*现在*为 "*自定义*"，如果作业以前运行过，则为 "*上次停止*"。 有关这些选项的详细信息，请参阅上文。

## <a name="visual-studio"></a>Visual Studio

在作业视图中，选择绿色箭头按钮以启动作业。 设置**作业输出启动模式**，然后选择 "**启动**"。 作业状态将更改为 "**正在运行**"。

**作业输出启动模式**有三个选项： *JobStartTime*、 *CustomTime*和*LastOutputEventTime*。 如果此属性不存在，则默认值为*JobStartTime*。 有关这些选项的详细信息，请参阅上文。


## <a name="powershell"></a>PowerShell

使用以下 cmdlet 通过 PowerShell 启动作业：

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

**OutputStartMode**有三个选项： *JobStartTime*、 *CustomTime*和*LastOutputEventTime*。 如果此属性不存在，则默认值为*JobStartTime*。 有关这些选项的详细信息，请参阅上文。

有关 `Start-AzStreamAnalyitcsJob` cmdlet 的详细信息，请查看[AzStreamAnalyticsJob 引用](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [快速入门：使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)
* [快速入门：使用适用于 Visual Studio 的 Azure 流分析工具创建流分析作业](stream-analytics-quick-create-vs.md)
