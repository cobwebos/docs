---
title: 如何启动 Azure Stream Analytics 作业
description: 本文介绍如何启动 Stream Analytics 作业。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9bc3e4132919e5fc5baadc78841e66efd3c34bcd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61362259"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>如何启动 Azure Stream Analytics 作业

在可以开始使用 Azure 门户、 Visual Studio 和 PowerShell 创建 Azure Stream Analytics 作业。 启动作业时，选择要启动创建输出的作业的时间。 Azure 门户、 Visual Studio 和 PowerShell 各有不同的方法来设置开始时间。 这些方法如下所述。

## <a name="start-options"></a>启动选项
三个以下选项可启动的作业。 请注意下面所述的所有时间都均中指定的那些[TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)。 如果未指定 TIMESTAMP BY，则将使用到达时间。
* **现在**:可以与相同流式处理作业开始时的起始点的输出事件。 如果使用临时运算符 （例如时间窗口，延迟或联接），Azure Stream Analytics 将自动重新查看输入源中的数据。 例如，如果"立即"启动作业，并且查询使用 5 分钟翻转窗口，Azure Stream Analytics 将查找从 5 分钟前的输入中的数据。
第一个可能的输出事件必须等于或晚于当前时间，时间戳和 ASA 保证所有逻辑上可能会导致输出的输入的事件已解决。 例如，会不生成任何部分的开窗聚合。 它始终是完整的聚合的值。

* **自定义**：可以选择输出的起始点。 类似于**现在**选项时，Azure Stream Analytics 会自动读取在此时间之前的数据如果在使用临时运算符 

* **上次停止时**。 如果作业以前已启动，但已手动停止或失败，此选项才可用。 选择此选项时 Azure Stream Analytics 将使用上次输出时间来重启该作业，因此不会丢失数据。 同样与上一个选项，Azure Stream Analytics 自动将如果使用临时运算符读取在此时间之前的数据。 由于多个输入的分区可能具有不同的时间，则使用的所有分区的最早停止时间，因此可能会在输出中看到一些重复项。 有关详细信息完全-一次处理为可在页面[事件的传递保证](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)。


## <a name="azure-portal"></a>Azure 门户

导航到在 Azure 门户并选择作业**启动**概述页上。 选择**作业输出开始时间**，然后选择**启动**。

选择的选项之一**作业输出开始时间**。 选项包括*现在*，*自定义*，和之前运行该作业，如果*上次停止时*。 有关这些选项的详细信息，请参阅上面。

## <a name="visual-studio"></a>Visual Studio

在作业视图中，选择绿色箭头按钮以启动作业。 设置**作业输出启动模式**，然后选择**启动**。 作业状态将更改为**运行**。

有三个选项用于**作业输出启动模式**:*JobStartTime*， *CustomTime*，和*LastOutputEventTime*。 如果缺少此属性，则默认值是*JobStartTime*。 有关这些选项的详细信息，请参阅上面。


## <a name="powershell"></a>PowerShell

使用以下 cmdlet 来开始使用 PowerShell 创建作业：

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

有三个选项用于**OutputStartMode**:*JobStartTime*， *CustomTime*，和*LastOutputEventTime*。 如果缺少此属性，则默认值是*JobStartTime*。 有关这些选项的详细信息，请参阅上面。

有关详细信息`Start-AzStreamAnalyitcsJob`cmdlet，查看[开始 AzStreamAnalyticsJob 引用](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [快速入门：创建使用 Azure PowerShell 的 Stream Analytics 作业](stream-analytics-quick-create-powershell.md)
* [快速入门：使用用于 Visual Studio 的 Azure 流分析工具创建流分析作业](stream-analytics-quick-create-vs.md)
