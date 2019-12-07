---
title: Azure 自动缩放疑难解答
description: 跟踪在 Service Fabric、虚拟机、Web 应用和云服务中使用的 Azure 自动缩放问题。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: robb
ms.subservice: autoscale
ms.openlocfilehash: 410c182075d0aa288ad05195958c396f1a357ff1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893563"
---
# <a name="troubleshooting-azure-autoscale"></a>Azure 自动缩放疑难解答
 
Azure Monitor 自动缩放功能可帮助你使用适当数量的资源来处理应用程序上的负载。 它可让你添加资源来处理负载增加，还可以通过删除处于空闲状态的资源来节省成本。 可以根据所选的计划、固定的日期时间或资源度量值进行缩放。 有关详细信息，请参阅[自动缩放概述](autoscale-overview.md)。

自动缩放服务提供了指标和日志，以了解已发生了哪些缩放操作，并评估了导致这些操作的条件。 你可以找到以下问题的答案：

- 为什么服务要横向扩展或处于扩展范围？
- 为什么服务无法缩放？
- 为什么自动缩放操作失败？
- 为什么自动缩放操作需要花费时间进行缩放？
  
## <a name="autoscale-metrics"></a>自动缩放指标

自动缩放提供了[四种指标](metrics-supported.md#microsoftinsightsautoscalesettings)来了解其操作。 

- **观察到的指标值**-你选择对其执行缩放操作的度量值（如显示或由自动缩放引擎计算得出）。 由于单个自动缩放设置可以具有多个规则和多个度量源，因此可以使用 "指标源" 作为维度进行筛选。
- **指标阈值**-设置为采用缩放操作的阈值。 由于单个自动缩放设置可以具有多个规则和多个度量源，因此可以使用 "指标规则" 作为维度进行筛选。
- **观察到的容量**-自动缩放引擎显示的目标资源的活动实例数。
- 已**启动的缩放操作**-自动缩放引擎启动的横向扩展和缩小操作的数目。 可以按横向扩展与缩放操作进行筛选。

您可以使用[指标资源管理器](metrics-getting-started.md)将上述指标绘制在一个位置。 图表应显示：

  - 实际指标
  - 由自动缩放引擎查看/计算的指标
  - 缩放操作的阈值
  - 容量更改 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>示例 1-分析简单自动缩放规则 

对于虚拟机规模集，我们有一个简单的自动缩放设置：

- 当集的平均 CPU 百分比大于 70% 10 分钟时向外扩展 
- 当集的 CPU 百分比小于5% 超过10分钟时进行缩放。 

我们来看一下自动缩放服务的指标。
 
![虚拟机规模集百分比 CPU 示例](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![虚拟机规模集百分比 CPU 示例](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***图 1a-虚拟机规模集的 CPU CPU 指标百分比和自动缩放设置的观测指标值指标***

![指标阈值和观察到的容量](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***图 1b-指标阈值和观察到的容量***

在图1b 中，扩展规则的**指标阈值**（浅蓝色线）为70。  **观察到的容量**（深蓝线）显示当前为3的活动实例数。 

> [!NOTE]
> 你将需要按 "指标触发规则维度扩大（增加）" 规则筛选**指标阈值**，以查看扩展阈值和按规则缩放（降低）。 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>示例 2-虚拟机规模集的高级自动缩放

我们有一个自动缩放设置，它允许虚拟机规模集资源根据其自身的指标**出站流**进行扩展。 请注意，会检查指标阈值的 "**按实例划分度量值**" 选项。 

缩放操作规则为： 

如果**每个实例的出站流**的值大于10，则自动缩放服务应按1个实例进行扩展。 

在这种情况下，自动缩放引擎的观察指标值是按实际指标值除以实例数来计算的。 如果观察到的指标值小于阈值，则不启动扩大操作。 
 
![虚拟机规模集自动缩放指标图表示例](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![虚拟机规模集自动缩放指标图表示例](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***图 2-虚拟机规模集自动缩放指标图表示例***

在图2中，可以看到两个度量值图表。 

顶部的图表显示**出站流**指标的实际值。 实际值为6。 

底部的图表显示了几个值。 
 - **观察到的指标值**（浅蓝色）为3，因为有2个活动实例，6除以2的值为3。 
 - **观察到的容量**（紫色）显示自动缩放引擎看到的实例计数。 
 - **指标阈值**（浅绿色）设置为10。 

如果有多个缩放操作规则，则可以使用度量资源管理器图表中的 "拆分" 或 "**添加筛选器**" 选项，按特定的源或规则查看指标。 有关拆分度量值图表的详细信息，请参阅[指标图表的高级功能-拆分](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>示例 3-了解自动缩放事件

在 "自动缩放设置" 屏幕中，打开 "**运行历史记录**" 选项卡以查看最新的缩放操作。 该选项卡还显示一段时间内**观察到的容量**的变化。 若要查找有关所有自动缩放操作的详细信息，包括更新/删除自动缩放设置等操作，请查看活动日志并按自动缩放操作进行筛选。

![自动缩放设置运行历史记录](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>自动缩放资源日志

与任何其他 Azure 资源相同，自动缩放服务提供[资源日志](resource-logs-overview.md)。 有两种类别的日志。

- **自动缩放评估**-每次执行检查时，自动缩放引擎记录每个单个条件评估的日志条目。  此条目包含有关所观察到的指标值、已评估的规则以及计算是否导致缩放操作的详细信息。

- **自动缩放操作**-引擎记录由自动缩放服务启动的缩放操作事件和这些缩放操作的结果（成功、失败以及自动缩放服务显示的缩放比例）。

与任何 Azure Monitor 支持的服务一样，你可以使用[诊断设置](diagnostic-settings.md)来路由这些日志：

- Log Analytics 工作区进行详细分析
- 到事件中心，然后再到非 Azure 工具
- 到 Azure 存储帐户进行存档  

![自动缩放诊断设置](media/autoscale-troubleshoot/diagnostic-settings.png)

上图显示 Azure 门户自动缩放诊断设置。 可以在其中选择 "诊断/资源日志" 选项卡，并启用日志收集和路由。 你还可以使用 REST API、CLI、PowerShell、资源管理器模板为诊断设置执行相同的操作，方法是选择 "资源类型" 作为 " *AutoscaleSettings*"。 

## <a name="troubleshooting-using-autoscale-logs"></a>使用自动缩放日志进行故障排除 

为了获得最佳的故障排除体验，建议你在创建自动缩放设置时通过工作区将日志路由到 Azure Monitor 日志（Log Analytics）。 上一部分中的图片显示了此过程。 您可以使用 Log Analytics 更好地验证评估和缩放操作。

将自动缩放日志配置为发送到 Log Analytics 工作区后，可以执行以下查询以检查日志。 

若要开始，请尝试使用此查询来查看最新的自动缩放评估日志：

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

或尝试使用以下查询来查看最新的缩放操作日志：

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

使用以下部分来解决这些问题。 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>发生了我不希望发生的缩放操作

首先执行缩放操作的查询，查找感兴趣的缩放操作。 如果它是最新的缩放操作，请使用以下查询：

```Kusto
AutoscaleScaleActionsLog
| take 1
```

从 "缩放操作" 日志中选择 CorrelationId 字段。 使用 CorrelationId 查找正确的评估日志。 执行下面的查询将显示计算出的所有规则和条件，并对该缩放操作求值。

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>什么配置文件导致了缩放操作？

已缩放操作已发生，但你的规则和配置文件重叠，需要跟踪导致此操作的情况。 

找到缩放操作的 correlationId （如示例1中所述），然后对评估日志执行查询来了解有关配置文件的详细信息。

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

使用以下查询，还可以更好地理解整个配置文件评估

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>未发生缩放操作

我期望缩放操作不会发生。 可能没有缩放操作事件或日志。

如果使用的是基于指标的缩放规则，请查看自动缩放指标。 观察到的**指标值**或观察到的**容量**可能不是你预期的值，因此未激发缩放规则。 你仍将看到评估，但不会看到扩展规则。 这也可能是因为冷却时间使缩放操作无法进行。 

 在预计缩放操作发生的时间段内查看自动缩放评估日志。 查看其进行的所有评估，并了解它为何决定不触发缩放操作。


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>缩放操作失败

在某些情况下，自动缩放服务执行缩放操作，但是系统决定不缩放或未能完成缩放操作。 使用此查询可查找失败的缩放操作。

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

创建警报规则，以获取自动缩放操作或失败的通知。 你还可以创建警报规则，以便在自动缩放事件时获得通知。

## <a name="schema-of-autoscale-resource-logs"></a>自动缩放资源日志的架构

有关详细信息，请参阅[自动缩放资源日志](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>后续步骤
阅读有关[自动缩放最佳实践](autoscale-best-practices.md)的信息。 