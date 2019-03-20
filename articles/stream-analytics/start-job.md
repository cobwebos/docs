---
title: 如何启动 Azure Stream Analytics 作业
description: 本文介绍如何启动 Stream Analytics 作业。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fb1d724907c09e2eb77930f5a235336ca8cd3a25
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886841"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>如何启动 Azure Stream Analytics 作业

在可以开始使用 Azure 门户、 Visual Studio 和 PowerShell 创建 Azure Stream Analytics 作业。 启动作业时，选择要启动创建输出的作业的时间。 Azure 门户、 Visual Studio 和 PowerShell 各有不同的方法来设置开始时间。 这些方法如下所述。

## <a name="azure-portal"></a>Azure 门户

导航到在 Azure 门户并选择作业**启动**概述页上。 选择**作业输出开始时间**，然后选择**启动**。

有三个选项用于**作业输出开始时间**:*现在*，*自定义*，和*上次停止时*。 选择*现在*在当前的时间启动作业。 选择*自定义*，可设置过去或未来的作业开始自定义的时间。 若要恢复已停止的作业不会丢失数据的情况下，选择*上次停止时*。

## <a name="visual-studio"></a>Visual Studio

在作业视图中，选择绿色箭头按钮以启动作业。 设置**作业输出启动模式**，然后选择**启动**。 作业状态将更改为**运行**。

有三个选项用于**作业输出启动模式**:*JobStartTime*， *CustomTime*，和*LastOutputEventTime*。 如果缺少此属性，则默认值是*JobStartTime*。

*JobStartTime*使输出事件流作为作业启动时相同的起始点。

*CustomTime*中指定的自定义时间开始输出*OutputStartTime*参数。

*LastOutputEventTime*使相同的最后一个事件输出时间的输出事件流的起始点。

## <a name="powershell"></a>PowerShell

使用以下 cmdlet 来开始使用 PowerShell 创建作业：

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

有三个选项用于**OutputStartMode**:*JobStartTime*， *CustomTime*，和*LastOutputEventTime*。 如果缺少此属性，则默认值是*JobStartTime*。

*JobStartTime*使输出事件流作为作业启动时相同的起始点。

*CustomTime*中指定的自定义时间开始输出*OutputStartTime*参数。

*LastOutputEventTime*使相同的最后一个事件输出时间的输出事件流的起始点。

有关详细信息`Start-AzStreamAnalyitcsJob`cmdlet，查看[开始 AzStreamAnalyticsJob 引用](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [快速入门：创建使用 Azure PowerShell 的 Stream Analytics 作业](stream-analytics-quick-create-powershell.md)
* [快速入门：使用用于 Visual Studio 的 Azure 流分析工具创建流分析作业](stream-analytics-quick-create-vs.md)
