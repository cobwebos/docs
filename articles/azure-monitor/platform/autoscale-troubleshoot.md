---
title: 故障排除 Azure 自动缩放
description: 跟踪服务结构、虚拟机、Web 应用和云服务中使用的 Azure 自动缩放问题。
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751336"
---
# <a name="troubleshooting-azure-autoscale"></a>故障排除 Azure 自动缩放
 
Azure 监视器自动缩放可帮助您运行适当数量的资源来处理应用程序中的负载。 它使您能够添加资源来处理负载的增加，并通过删除闲置的资源来节省资金。 您可以根据您选择的计划、固定日期时间或资源指标进行缩放。 有关详细信息，请参阅[自动缩放概述](autoscale-overview.md)。

自动缩放服务为您提供指标和日志，以了解发生了哪些缩放操作，以及评估导致这些操作的条件。 您可以找到问题的答案，例如：

- 为什么我的服务正在扩展或扩展？
- 为什么我的服务没有扩展？
- 为什么自动缩放操作失败？
- 为什么自动缩放操作需要时间进行缩放？
  
## <a name="autoscale-metrics"></a>自动缩放指标

自动缩放为您提供了[四个指标](metrics-supported.md#microsoftinsightsautoscalesettings)来了解其操作。 

- **观察指标值**- 选择对其执行缩放操作的指标值，如自动缩放引擎看到或计算的那样。 由于单个自动缩放设置可以有多个规则，因此有多个指标源，因此可以使用"指标源"作为维度进行筛选。
- **公制阈值**- 您设置为执行缩放操作的阈值。 由于单个自动缩放设置可以有多个规则，因此有多个指标源，因此可以使用"度量规则"作为维度进行筛选。
- **观测到的容量**- 自动缩放引擎所看到的目标资源的活动实例数。
- **启动的缩放操作** - 自动缩放引擎启动的横向扩展操作和横向缩小操作的数目。 您可以按横向扩展与缩放来筛选操作。

您可以使用[指标资源管理器](metrics-getting-started.md)在一个位置绘制上述指标。 图表应显示：

  - 实际指标
  - 自动缩放引擎看到的/计算的指标
  - 缩放操作的阈值
  - 容量的变化 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>示例 1 - 分析简单的自动缩放规则 

我们为虚拟机缩放集提供了一个简单的自动缩放设置，该设置：

- 当集的平均 CPU 百分比大于 70% 时，10 分钟内扩展 
- 当集的 CPU 百分比小于 5%时，在 10 分钟内缩放。 

让我们查看自动缩放服务的指标。
 
![虚拟机规模设置百分比 CPU 示例](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![虚拟机规模设置百分比 CPU 示例](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***图 1a - 虚拟机规模集的 CPU 百分比指标和自动缩放设置的观察指标值指标***

![公制阈值和观察到的容量](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***图 1b - 公制阈值和观测容量***

在图 1b 中，横向扩展规则的**公制阈值**（浅蓝线）为 70。  **"观察容量**"（深蓝色线）显示活动实例的数量，当前为 3。 

> [!NOTE]
> 您需要按指标触发器规则维度横向横向扩展（增加）规则筛选**指标阈值**，以查看横向扩展阈值和规则中的刻度（减少）。 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>示例 2 - 虚拟机规模集的高级自动缩放

我们有一个自动缩放设置，允许虚拟机缩放集资源根据其自己的指标**出站流**进行横向扩展。 请注意，检查指标阈值的**除法指标（按实例计数**选项）。 

缩放操作规则为： 

如果**每个实例的出站流**值大于 10，则自动缩放服务应扩展 1 个实例。 

在这种情况下，自动缩放引擎的观测指标值计算为实际指标值除以实例数。 如果观察到的指标值小于阈值，则不启动横向扩展操作。 
 
![虚拟机规模集自动缩放指标图表示例](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![虚拟机规模集自动缩放指标图表示例](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***图 2 - 虚拟机规模集自动缩放指标图表示例***

在图 2 中，您可以看到两个指标图表。 

顶部的图表显示**出站流**指标的实际值。 实际值为 6。 

底部的图表显示几个值。 
 - **观测指标值**（浅蓝色）为 3，因为有 2 个活动实例，6 除以 2 是 3。 
 - **"观测容量**"（紫色）显示自动缩放引擎看到的实例计数。 
 - **公制阈值**（浅绿色）设置为 10。 

如果存在多个缩放操作规则，则可以使用"指标资源管理器"图表中的拆分或**添加筛选器**选项按特定源或规则查看指标。 有关拆分指标图表的详细信息，请参阅[指标图表的高级功能 - 拆分](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>示例 3 - 了解自动缩放事件

在自动缩放设置屏幕中，转到 **"运行历史记录**"选项卡以查看最新的缩放操作。 该选项卡还显示**观察容量**随时间的变化。 要查找有关所有自动缩放操作（包括更新/删除自动缩放设置等操作）的更多详细信息，请查看活动日志，并通过自动缩放操作进行筛选。

![自动缩放设置运行历史记录](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>自动缩放资源日志

与任何其他 Azure 资源相同，自动缩放服务提供[资源日志](platform-logs-overview.md)。 日志分为两类。

- **自动缩放评估**- 自动缩放引擎记录每次进行检查时每个状态评估的日志条目。  该条目包括指标的观察值、评估的规则以及评估是否导致缩放操作的详细信息。

- **自动缩放缩放操作**- 引擎记录自动缩放服务启动的操作事件以及这些缩放操作的结果（成功、失败以及自动缩放服务所看到的缩放量）。

与任何 Azure 监视器支持的服务一样，您可以使用["诊断设置"](diagnostic-settings.md)路由这些日志：

- 到日志分析工作区进行详细分析
- 到事件中心，然后到非 Azure 工具
- 到 Azure 存储帐户进行存档  

![自动缩放诊断设置](media/autoscale-troubleshoot/diagnostic-settings.png)

上图显示了 Azure 门户自动缩放的诊断设置。 在那里，您可以选择诊断/资源日志选项卡，并启用日志收集和路由。 您还可以使用 REST API、CLI、PowerShell、资源管理器模板执行相同的操作，用于诊断设置，选择与*Microsoft.Insights/自动缩放设置相同的*资源类型。 

## <a name="troubleshooting-using-autoscale-logs"></a>使用自动缩放日志进行故障排除 

为了获得最佳故障排除体验，我们建议您在创建自动缩放设置时通过工作区将日志路由到 Azure 监视器日志（日志分析）。 此过程显示在上一节中的图片中。 您可以使用日志分析更好地验证评估并缩放操作。

将自动缩放日志配置为发送到日志分析工作区后，可以执行以下查询以检查日志。 

要开始，请尝试此查询以查看最新的自动缩放评估日志：

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

或者尝试以下查询以查看最新的缩放操作日志：

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

使用以下部分来解决这些问题。 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>发生了一个我没想到的缩放操作

首先执行缩放操作的查询，以查找您感兴趣的缩放操作。 如果是最新的缩放操作，请使用以下查询：

```Kusto
AutoscaleScaleActionsLog
| take 1
```

从缩放操作日志中选择"关联 Id"字段。 使用"关联 Id"查找正确的评估日志。 执行以下查询将显示评估的所有规则和条件，从而导致该缩放操作。

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>什么配置文件导致了缩放操作？

发生了缩放的操作，但规则和配置文件重叠，需要跟踪导致该操作的原因。 

查找缩放操作的相关性 Id（如示例 1 中所述），然后在评估日志上执行查询以了解有关配置文件的更多信息。

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

我期望一个规模行动，它没有发生。 可能没有缩放操作事件或日志。

如果使用基于指标的比例规则，请查看自动缩放指标。 "**观察"指标值**或 **"观察到的容量**"可能不是您期望的，因此比例规则没有触发。 您仍然会看到评估，但不会看到横向扩展规则。 冷却时间也可能阻止缩放操作发生。 

 查看预期发生缩放操作的时间段内的自动缩放评估日志。 审查它所做的所有评估，以及为什么它决定不触发规模行动。


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>缩放操作失败

可能存在自动缩放服务执行规模操作，但系统决定不缩放或未能完成缩放操作的情况。 使用此查询可以查找失败的缩放操作。

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

创建警报规则以收到自动缩放操作或失败的通知。 您还可以创建警报规则以获取有关自动缩放事件的通知。

## <a name="schema-of-autoscale-resource-logs"></a>自动缩放资源日志的架构

有关详细信息，请参阅[自动缩放资源日志](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>后续步骤
阅读有关[自动缩放最佳实践](autoscale-best-practices.md)的信息。 
