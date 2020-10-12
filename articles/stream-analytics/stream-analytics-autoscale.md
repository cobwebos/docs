---
title: 自动缩放流分析作业
description: 本文介绍如何根据预定义计划或作业指标值自动缩放流分析作业
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: 07cbb28b98fcbac1932424c1c72f388813ec2400
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86037556"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>使用 Azure 自动化服务自动缩放流分析作业

可以通过配置自动缩放来优化流分析作业的成本。 自动缩放可根据输入负载的变化增加或减少作业的流单元 (SU)。 你可以根据需要纵向扩展或纵向缩减，而不必过度预配作业。 可通过两种方式将作业配置为自动进行缩放：
1. 如果输入负载可预测，则**预定义计划**。 例如，你预计白天发生输入事件的几率更高，并且你希望使用更多 SU 来运行作业。
2. 如果输入负载不可预测，则**根据作业指标触发纵向扩展和纵向缩减操作**。 可以根据作业指标（例如输入事件数或积压的输入事件数）动态更改 SU 的数量。

## <a name="prerequisites"></a>先决条件
在开始为作业配置自动缩放之前，请完成以下步骤。
1. 将作业优化为具有[并行拓扑](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)。 如果可以在作业运行时更改其规模，则表示作业具有并行拓扑，可以配置为自动进行缩放。
2. [创建启用了“RunAsAccount”选项的 Azure 自动化帐户](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)。 此帐户必须有权管理流分析作业。

## <a name="set-up-azure-automation"></a>设置 Azure 自动化
### <a name="configure-variables"></a>配置变量
在 Azure 自动化帐户中添加以下变量。 这些变量将用于后续步骤中所述的 runbook。

| 名称 | 类型 | Value |
| --- | --- | --- |
| **jobName** | String | 要自动缩放的流分析作业的名称。 |
| **resourceGroupName** | String | 作业所在资源组的名称。 |
| **subId** | String | 作业所在的订阅 ID。 |
| **increasedSU** | Integer | 希望作业按计划缩放到的较高 SU 值。 此值必须是作业运行时其“缩放”设置中显示的有效 SU 选项之一。 |
| **decreasedSU** | Integer | 希望作业按计划缩放到的较低 SU 值。 此值必须是作业运行时其“缩放”设置中显示的有效 SU 选项之一。 |
| **maxSU** | Integer | 按负载自动缩放时，你希望作业逐步缩放到的最大 SU 值。 此值必须是作业运行时其“缩放”设置中显示的有效 SU 选项之一。 |
| **minSU** | Integer | 按负载自动缩放时，你希望作业逐步缩放到的最小 SU 值。 此值必须是作业运行时其“缩放”设置中显示的有效 SU 选项之一。 |

![在 Azure 自动化中添加变量](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>创建 Runbook
下一步是创建两个 PowerShell runbook。 一个用于纵向扩展操作，另一个用于纵向缩减操作。
1. 在 Azure 自动化帐户中，转到“流程自动化”下的“Runbook”，然后选择“创建 Runbook”。
2. 将类型设置为 PowerShell 的第一个 runbook 命名为 ScaleUpRunbook。 使用 GitHub 中可用的 [ScaleUpRunbook PowerShell 脚本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1)。 保存并发布它。
3. 使用 PowerShell 类型创建另一个名为 ScaleDownRunbook 的 runbook。 使用 GitHub 中可用的 [ScaleDownRunbook PowerShell 脚本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1)。 保存并发布它。

![在 Azure 自动化中自动缩放 runbook](./media/autoscale/runbooks.png)

现在，你拥有可对流分析作业自动触发纵向扩展和纵向缩减操作的 runbook。 这些 runbook 可以使用预定义计划触发，也可以根据作业指标动态设置。

## <a name="autoscale-based-on-a-schedule"></a>按计划自动缩放
Azure 自动化允许你配置 runbook 触发计划。
1. 在 Azure 自动化帐户中，选择“共享资源”下的“计划”。 然后，选择“添加计划”。
2. 例如，你可以创建两个计划。 一个表示你希望何时纵向扩展作业，另一个表示你希望何时纵向缩减作业。 你可以为这些计划定义重复周期。

   ![Azure 自动化中的计划](./media/autoscale/schedules.png)

3. 打开“ScaleUpRunbook”，在“资源”下选择“计划”。 然后，可以将 runbook 链接到在先前步骤中创建的计划。 可以将多个计划链接到同一个 runbook，当你想要在一天的不同时间运行相同的缩放操作时，这一点很有用。

![在 Azure 自动化中计划 runbook](./media/autoscale/schedulerunbook.png)

1. 对 ScaleDownRunbook 重复上面的步骤。

## <a name="autoscale-based-on-load"></a>根据负载自动缩放
在某些情况下，你无法预测输入负载。 在这种情况下，比较好的做法是在最小和最大边界内逐步纵向扩展/缩减。 你可以在流分析作业中配置警报规则，以在作业指标高于或低于阈值时触发 runbook。
1. 在 Azure 自动化帐户中，再创建两个名为 minSU 和 maxSU 的整数变量。 这会设置作业逐步缩放的边界。
2. 创建两个新的 runbook。 可以使用 [StepScaleUp PowerShell 脚本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1)，该脚本以递增的方式增加作业的 SU，直到达到 maxSU 值为止。 也可以使用 [StepScaleDown PowerShell 脚本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1)，该脚本逐步减少作业的 SU，直到达到 minSU 值为止。 或者，如果要缩放到特定的 SU 值，可以使用上一部分中的 runbook。
3. 在流分析作业中，选择“监视”下的“警报规则”。 
4. 创建两个操作组。 一个用于纵向扩展操作，另一个用于纵向缩减操作。 选择“管理操作”，然后单击“添加操作组”。 
5. 填写所需的字段。 选择“操作类型”时，选择“自动化 Runbook”。 选择要在警报触发时触发的 runbook。 然后，创建操作组。

   ![创建操作组](./media/autoscale/create-actiongroup.png)
6. 在作业中创建一个[新的警报规则](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal)。 根据所选指标指定条件。 建议使用[输入事件、SU% 利用率或积压的输入事件数](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)指标来定义自动缩放逻辑  。 在触发纵向扩展操作时，还建议使用 1 分钟聚合粒度和评估频率。 这样做可确保作业有充足的资源来应对输入量高峰。
7. 选择上一步中创建的操作组，然后创建警报。
8. 对要根据作业指标条件触发的任何其他缩放操作重复步骤 2 到 4。

最佳做法是在生产环境中运行作业之前，先运行缩放测试。 在针对不同的输入负载测试作业时，你会了解到，作业需要多少个 SU 来实现不同的输入吞吐量。 这可以告知你在警报规则中定义的触发纵向扩展和纵向缩减操作的条件。 

## <a name="next-steps"></a>后续步骤
* [在 Azure 流分析中创建可并行的查询](stream-analytics-parallelization.md)
* [扩展 Azure 流分析作业以增加吞吐量](stream-analytics-scale-jobs.md)
