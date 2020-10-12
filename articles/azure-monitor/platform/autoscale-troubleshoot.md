---
title: 排查 Azure 自动缩放问题
description: 追查到 Service Fabric、虚拟机、Web 应用和云服务中使用的 Azure 自动缩放的问题。
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: a29b5d11a6ea06af9d5b6a8b5120c6f0caa6601e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90979045"
---
# <a name="troubleshooting-azure-autoscale"></a>排查 Azure 自动缩放问题
 
Azure Monitor 自动缩放可帮助你运行适当数量的资源来处理应用程序的负载。 在负载增加时，可以通过自动缩放添加资源来处理增加的负载；在资源处于空闲状态时，可以通过自动缩放删除资源来节省资金。 可以根据你选择的计划、固定的日期时间或资源指标进行缩放。 有关详细信息，请参阅[自动缩放概述](autoscale-overview.md)。

自动缩放服务提供了指标和日志，以便你了解已发生了哪些缩放操作，它还可以评估导致执行这些操作的条件。 你可以找到下面所述的问题的答案：

- 为何横向扩展或缩减了我的服务？
- 我的服务为何未缩放？
- 自动缩放操作为何失败？
- 自动缩放操作为何花费了很长时间？
  
## <a name="autoscale-metrics"></a>自动缩放指标

自动缩放提供了[四个指标](metrics-supported.md#microsoftinsightsautoscalesettings)以便你了解其操作。 

- **观察到的指标值** - 你选择针对其执行缩放操作的指标的值，由自动缩放引擎查看或计算。 由于一个自动缩放设置可以包含多个规则（因而也包含多个指标源），你可以使用“指标源”作为维度进行筛选。
- **指标阈值** - 设置的阈值，达到该阈值即会执行缩放操作。 由于一个自动缩放设置可以包含多个规则（因而也包含多个指标源），你可以使用“指标规则”作为维度进行筛选。
- **观察到的容量** - 自动缩放引擎看到的目标资源活动实例数。
- **启动的缩放操作** - 自动缩放引擎启动的横向扩展操作和横向缩小操作的数目。 可按横向扩展与横向缩减操作进行筛选。

可以使用[指标资源管理器](metrics-getting-started.md)在一个位置绘制上述所有指标的图表。 该图表应显示：

  - 实际指标
  - 自动缩放引擎看到/计算的指标
  - 缩放操作的阈值
  - 容量变化 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>示例 1 - 分析简单的自动缩放规则 

我们对虚拟机规模集使用了一个简单的自动缩放设置：

- 如果某个规模集的平均 CPU 利用率百分比持续 10 分钟大于 70%，则横向扩展 
- 如果该规模集的 CPU 利用率百分比持续 10 分钟以上小于 5%，则横向缩减。 

让我们查看自动缩放服务提供的指标。
 
![屏幕截图显示了虚拟机规模集百分比 CPU 示例。](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![虚拟机规模集 CPU 利用率百分比示例](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

图 1a - 虚拟机规模集的 CPU 利用率百分比指标，以及自动缩放设置的“观测到的指标值”指标

![“指标阈值”和“观察到的容量”](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

图 1b -“指标阈值”和“观察到的容量”

在图 1b 中，横向扩展规则的“指标阈值”（浅蓝色线条）为 70。  “观察到的容量”（深蓝色线条）显示活动实例数，当前为 3。 

> [!NOTE]
> 需要按指标触发器规则维度横向扩展（递增）规则筛选“指标阈值”，才能查看横向扩展阈值。 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>示例 2 - 虚拟机规模集的高级自动缩放

我们使用了一个自动缩放设置来使虚拟机规模集资源能够根据其自己的指标“出站流”进行横向扩展。 请注意，已选中了指标阈值的“将指标除以实例计数”选项。 

缩放操作规则为： 

如果“每个实例的出站流”值大于 10，则应将自动缩放服务横向扩展 1 个实例。 

在这种情况下，自动缩放引擎的“观察到的指标值”的计算方式是将实际指标值除以实例数。 如果观察到的指标值小于阈值，则不启动横向扩展操作。 
 
![屏幕截图显示 "平均出站流" 页，其中包含虚拟机规模集自动缩放指标图表的示例。](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![虚拟机规模集自动缩放指标图表示例](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

图 2 - 虚拟机规模集自动缩放指标图表示例

在图 2 中可以看到两个指标图表。 

顶部的图表显示“出站流”指标的实际值。 实际值为 6。 

底部的图表显示了少数几个值。 
 - “观察到的指标值”（浅蓝色）为 3，因为有 2 个活动实例，6 除以 2 等于 3。 
 - “观察到的容量”（紫色）显示自动缩放引擎看到的实例计数。 
 - “指标阈值”（浅绿色）设置为 10。 

如果有多个缩放操作规则，则可以使用指标资源管理器图表中的“拆分”或“添加筛选器”选项，按特定的源或规则查看指标。 有关拆分指标图表的详细信息，请参阅[指标图表的高级功能 - 拆分](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>示例 3 - 了解自动缩放事件

在自动缩放设置屏幕上，转到“运行历史记录”选项卡查看最近的缩放操作。 该选项卡还会显示“观察到的容量”在一段时间内的变化。 若要查找有关所有自动缩放操作（包括更新/删除自动缩放设置等操作）的详细信息，请查看活动日志并按自动缩放操作进行筛选。

![自动缩放设置 - 运行历史记录](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>自动缩放资源日志

与任何其他 Azure 资源相同，自动缩放服务提供[资源日志](platform-logs-overview.md)。 有两种类别的日志。

- **自动缩放评估** - 自动缩放引擎每次执行检查时，都会针对每一条件评估记录相应的日志条目。  该条目包含有关所观察到的指标值、评估的规则以及该评估是否导致发生缩放操作的详细信息。

- **“自动缩放”缩放操作** - 引擎将会记录自动缩放服务发起的缩放操作事件，以及这些缩放操作的结果（成功、失败，以及自动缩放服务看到的缩放量）。

与在 Azure Monitor 支持的任何服务中一样，你可以使用[诊断设置](diagnostic-settings.md)将这些日志：

- 路由到 Log Analytics 工作区进行详细分析
- 依次路由到事件中心和非 Azure 工具
- 路由到 Azure 存储帐户进行存档  

![自动缩放诊断设置](media/autoscale-troubleshoot/diagnostic-settings.png)

上图显示了 Azure 门户中的自动缩放诊断设置。 在其中可以选择“诊断/资源日志”选项卡，并启用日志收集和路由。 还可以使用 REST API、CLI、PowerShell、资源管理器模板，通过选择“Microsoft.Insights/AutoscaleSettings”作为资源类型，来对诊断设置执行相同的操作。 

## <a name="troubleshooting-using-autoscale-logs"></a>使用自动缩放日志进行故障排除 

为了获得最佳的故障排除体验，我们建议在创建自动缩放设置时，通过工作区将日志路由到 Azure Monitor 日志 (Log Analytics)。 上一部分的插图已演示了此过程。 使用 Log Analytics 可以更好地验证评估和缩放操作。

将自动缩放日志配置为发送到 Log Analytics 工作区后，可以执行以下查询来检查日志。 

若要开始，请尝试使用此查询来查看最近的自动缩放评估日志：

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

或者尝试使用以下查询来查看最近的缩放操作日志：

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

如果遇到任何问题，请参考以下部分。 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>发生了意料之外的缩放操作

首先对缩放操作执行查询，找到你所关注的缩放操作。 如果它是最新的缩放操作，请使用以下查询：

```Kusto
AutoscaleScaleActionsLog
| take 1
```

在缩放操作日志中选择 CorrelationId 字段。 使用 CorrelationId 查找正确的评估日志。 执行以下查询会显示已评估的、导致发生该缩放操作的所有规则和条件。

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>哪个配置文件导致发生了缩放操作？

发生了缩放操作，但你的规则和配置文件重叠，需要追查到哪一个配置文件导致发生了该操作。 

找到缩放操作的 correlationId（如示例 1 中所述），然后对评估日志执行查询以了解有关该配置文件的详细信息。

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

也可以使用以下查询来更好地了解整个配置文件评估

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>未发生缩放操作

我预期会发生某个缩放操作，但它并未发生。 可能没有缩放操作事件或日志。

如果你使用的是基于指标的缩放规则，请查看自动缩放指标。 “观察到的指标值”或“观察到的容量”可能不符合预期，因此未激发缩放规则。  你仍然可以看到评估，但看不到横向扩展规则。 此外，冷却时间也可能会阻止发生缩放操作。 

 查看你预期会发生缩放操作的时间段内的自动缩放评估日志。 查看自动缩放服务执行的所有评估，确定它为何决定不触发缩放操作。


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>缩放操作失败

在某些情况下，自动缩放服务会执行缩放操作，但系统决定不进行缩放或者无法完成缩放操作。 使用此查询来查找失败的缩放操作。

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

创建警报规则，以便在发生自动缩放操作或者失败时获得通知。 创建警报规则还可以在发生自动缩放事件时获得通知。

## <a name="schema-of-autoscale-resource-logs"></a>自动缩放资源日志的架构

有关详细信息，请参阅[自动缩放资源日志](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>后续步骤
阅读有关[自动缩放最佳做法](autoscale-best-practices.md)的信息。 
