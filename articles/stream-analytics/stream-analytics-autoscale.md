---
title: 自动缩放流分析作业
description: 本文介绍如何基于预定义的计划或作业指标值自动缩放流分析作业
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: 07cbb28b98fcbac1932424c1c72f388813ec2400
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037556"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>使用 Azure 自动化自动缩放流分析作业

可以通过配置自动缩放来优化流分析作业的成本。 自动缩放可增加或减少作业的流式处理单位（SUs），以匹配输入负载的变化。 你可以根据需要增加或减少作业，而无需过度预配作业。 可通过两种方式将作业配置为自动缩放：
1. 当你具有可预测的输入负载时，**预先定义计划**。 例如，你预计在白天需要更高的输入事件率，并希望你的作业运行更多的 SUs。
2. 当没有可预测的输入负载时，**根据作业指标触发增加和缩减操作**。 可以根据作业指标（如输入事件数或囤积的输入事件），动态更改 SUs 的数目。

## <a name="prerequisites"></a>先决条件
在开始配置作业的自动缩放之前，请完成以下步骤。
1. 作业经过优化，可具有[并行拓扑](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)。 如果在作业运行时可以更改其规模，则作业将具有并行拓扑，并且可以配置为自动缩放。
2. 创建启用了 "RunAsAccount" 选项的[Azure 自动化帐户](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)。 此帐户必须有权管理流分析作业。

## <a name="set-up-azure-automation"></a>设置 Azure 自动化
### <a name="configure-variables"></a>配置变量
在 Azure 自动化帐户中添加以下变量。 这些变量将在后续步骤中描述的 runbook 中使用。

| 名称 | 类型 | 值 |
| --- | --- | --- |
| **jobName** | String | 要自动缩放的流分析作业的名称。 |
| **resourceGroupName** | String | 其中存在作业的资源组的名称。 |
| **subId** | String | 其中存在作业的订阅 ID。 |
| **increasedSU** | Integer | 希望作业按计划缩放到的更高 SU 值。 此值必须是在作业运行时在作业的**缩放**设置中看到的有效 SU 选项之一。 |
| **decreasedSU** | Integer | 希望作业按计划缩放到的较低的 SU 值。 此值必须是在作业运行时在作业的**缩放**设置中看到的有效 SU 选项之一。 |
| **maxSU** | Integer | 当通过负载自动缩放时，你希望作业根据步骤扩展到的最大 SU 值。 此值必须是在作业运行时在作业的**缩放**设置中看到的有效 SU 选项之一。 |
| **minSU** | Integer | 当通过负载自动缩放时，你希望作业根据步骤扩展到的最小 SU 值。 此值必须是在作业运行时在作业的**缩放**设置中看到的有效 SU 选项之一。 |

![在 Azure 自动化中添加变量](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>创建 Runbook
下一步是创建两个 PowerShell runbook。 一个用于纵向扩展，另一个用于缩小操作。
1. 在 Azure 自动化帐户中，在 "**流程自动化**" 下，选择 " **runbook** "，并选择 "**创建 Runbook**"。
2. 将类型设置为 PowerShell 的第一个 runbook *ScaleUpRunbook*的名称设置为。 使用 GitHub 中提供的[ScaleUpRunbook PowerShell 脚本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1)。 保存并发布它。
3. 创建另一个名为*ScaleDownRunbook*的 runbook，其类型为 PowerShell。 使用 GitHub 中提供的[ScaleDownRunbook PowerShell 脚本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1)。 保存并发布它。

![Azure Automation 中的自动缩放 runbook](./media/autoscale/runbooks.png)

现在，你的 runbook 可以自动触发你的流分析作业的向上缩放和向下缩放操作。 可以使用预定义的计划触发这些 runbook，也可以根据作业指标进行动态设置。

## <a name="autoscale-based-on-a-schedule"></a>按计划自动缩放
Azure 自动化允许配置计划以触发 runbook。
1. 在 Azure 自动化帐户中，选择 "**共享资源**" 下的 "**计划**"。 然后，选择 "**添加计划**"。
2. 例如，可以创建两个计划。 一个，它表示你希望作业纵向扩展的时间，另一个表示你希望作业向下扩展的时间。 可以为这些计划定义定期。

   ![Azure 自动化中的计划](./media/autoscale/schedules.png)

3. 打开**ScaleUpRunbook** ，然后在 "**资源**" 下选择 "**计划**"。 然后，可以将 runbook 链接到在前面的步骤中创建的计划。 可以将多个计划链接到同一个 runbook，当你想要在一天的不同时间运行相同的缩放操作时，这会很有用。

![在 Azure 自动化中计划 runbook](./media/autoscale/schedulerunbook.png)

1. 针对**ScaleDownRunbook**重复上述步骤。

## <a name="autoscale-based-on-load"></a>根据负载自动缩放
在某些情况下，你无法预测输入负载。 在这种情况下，更好的做法是在最小和最大界限的步骤中增加/减少。 你可以在流分析作业中配置警报规则，以便在作业指标高于或低于阈值时触发 runbook。
1. 在 Azure 自动化帐户中创建两个更多的整数变量，称为**minSU**和**maxSU**。 这会在步骤中设置作业的缩放范围。
2. 创建两个新的 runbook。 你可以使用[StepScaleUp PowerShell 脚本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1)，增加作业的 SUs 增量，直到**maxSU**值。 你还可以使用[StepScaleDown PowerShell 脚本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1)在步骤中降低作业的 SUs，直到达到**minSU**值。 或者，如果您要缩放到的特定 SU 值，则可以使用上一节中的 runbook。
3. 在流分析作业中，选择 "**监视**" 下的 "**警报规则**"。 
4. 创建两个操作组。 一个用于向上扩展操作，另一个用于向下缩放操作。 选择 "**管理操作**"，然后单击 "**添加操作组**"。 
5. 填写所需的字段。 选择 "**操作类型**" 时，请选择 "**自动化 Runbook** "。 选择触发警报时要触发的 runbook。 然后，创建操作组。

   ![创建操作组](./media/autoscale/create-actiongroup.png)
6. 在作业中创建[**新的警报规则**](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal)。 根据所选的指标指定条件。 [*输入事件*、 *SU% 使用率*或*囤积输入事件*](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)是建议用于定义自动缩放逻辑的指标。 还建议在触发扩展操作时使用1分钟的*聚合粒度*和*计算频率*。 这样做可确保作业有充足的资源来应对输入卷中的巨大高峰。
7. 选择上一步中创建的操作组，然后创建警报。
8. 对于想要基于作业指标的条件触发的任何其他缩放操作，请重复步骤2到4。

最佳做法是在生产中运行作业之前运行规模测试。 当你针对不同的输入负载测试你的作业时，你可以了解作业需要多少个 SUs 来实现不同的输入吞吐量。 这可以通知你在警报规则中定义的条件，这些条件触发向上扩展和向下缩放操作。 

## <a name="next-steps"></a>后续步骤
* [在 Azure 流分析中创建可并行的查询](stream-analytics-parallelization.md)
* [缩放 Azure 流分析作业以增加吞吐量](stream-analytics-scale-jobs.md)
