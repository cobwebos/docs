---
title: "通过 Azure 门户手动或自动缩放实例计数 | Microsoft Docs"
description: "了解如何在 Azure 中缩放服务。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: d171538ea57839eccddcc74ca099a39aee34ea10
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---
# <a name="scale-instance-count-manually-or-automatically"></a>手动或自动缩放实例计数
在 [Azure 门户](https://portal.azure.com/)中，可手动设置服务的实例计数，或者设置参数以基于需求自动缩放。 这通常称为扩大或缩小。

基于实例计数进行缩放之前，应考虑的除了实例计数之外，缩放还会受**定价层**影响。 不同定价层可以具有不同数量的核心和内存，因此它们对于相同数量的实例具有更佳性能（即增加或减少）。 本文专门介绍缩小和扩大。

可以在门户中进行缩放，也可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx) 或 [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) 手动或自动调整规模。

> [!NOTE]
> 本文介绍如何在门户 [http://portal.azure.com](http://portal.azure.com) 中创建自动缩放设置。不可在经典门户 ([http://manage.windowsazure.com](http://manage.windowsazure.com)) 中对此门户中创建的自动缩放设置进行编辑。
> 
> 

## <a name="scaling-manually"></a>手动缩放
1. 在 [Azure 门户](https://portal.azure.com/)中，单击“浏览”，并导航到要缩放的资源（如“应用服务计划”）。
2. 单击“设置”>“扩大(应用服务计划)”。
3. 在“缩放”边栏选项卡顶部，可以查看服务的自动缩放操作历史记录。
   
    ![“缩放”分页](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
   
   > [!NOTE]
   > 只有自动缩放执行的操作才会显示在此图表中。 如果手动调整实例计数，则更改不会反映在此图表中。
   > 
   > 
4. 可以使用滑块手动调整“实例”数。
5. 单击“保存”命令，可几乎立即缩放到该实例数。

## <a name="scaling-based-on-a-pre-set-metric"></a>基于预设指标进行缩放
如果要基于指标自动调整实例数，请在“缩放依据”下拉列表中选择所需指标。 例如，对于“应用服务计划”，可以按“CPU 百分比”进行缩放。

1. 选择指标时，会获得一个滑块和/或文本框，以输入要在其间进行缩放的实例数：
   
    ![显示了 CPU 百分比的“缩放”分页](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)
   
    自动缩放绝不会使服务低于或高于所设置的边界（无论负载多少）。
2. 其次，为指标选择目标范围。 例如，如果选择“CPU 百分比”，则可以为服务中所有实例间的平均 CPU 设置目标。 当平均 CPU 超过定义的最大值时会进行扩大，同样，每当平均 CPU 下降到低于最小值时会进行缩小。
3. 单击“保存”命令。 自动缩放会每隔几分钟进行检查，以确保处于指标的实例范围和目标内。 当服务收到更多流量时，会获得更多实例而无需执行任何操作。

## <a name="scale-based-on-other-metrics"></a>基于其他指标的缩放
可以基于“缩放依据”下拉列表中显示的预设值之外的指标进行缩放，甚至可以具有一组复杂的扩大和缩小规则。

### <a name="adding-or-changing-a-rule"></a>添加或更改规则
1. 在“缩放依据”下拉列表中选择“计划和性能规则”：![性能规则](./media/insights-how-to-scale/Insights_PerformanceRules.png)
2. 如果以前进行过自动缩放，则你会看到所具有的确切规则的视图。
3. 若要基于其他指标进行缩放，请单击“添加规则”行。 还可单击一个现有行，以便从以前具有的指标更改为要作为缩放依据的指标。
   ![添加规则](./media/insights-how-to-scale/Insights_AddRule.png)
4. 现在需要选择要作为缩放依据的指标。 选择指标时，需要考虑几个事项：
   
   * 指标所来自的资源。 通常，这会与所缩放的资源相同。 但是，如果要按存储队列的深度进行缩放，则资源是要作为缩放依据的队列。
   * 指标名称本身。
   * 指标的时间聚合。 这是在持续时间内合并数据的方式。
5. 选择指标之后，会为指标选择阈值以及运算符。 例如，可以设置“大于”“80%”。
6. 然后选择要执行的操作。 有几种不同类型的操作：
   
   * 增加或减少某个数量 — 这会添加或删除定义的实例数“值”
   * 增加或减少百分比 — 这会按百分比更改实例数。 例如，可以在“值”字段中填写 25，如果当前具有 8 个实例，则会添加 2 个实例。
   * 增加或减少至 — 这会将实例计数设置为定义的“值”。
7. 最后，可以选择冷却 — 前一缩放操作之后，此规则应等待多长时间才能再次缩放。
8. 配置规则之后，点击“确定”。
9. 配置了所有所需规则之后，请务必点击“保存”命令。

### <a name="scaling-with-multiple-steps"></a>使用多个步骤进行缩放
以上示例非常基本。 但是，如果要更积极地进行地增加（或减少），甚至可以为相同指标添加多个缩放规则。 例如，可以对 CPU 百分比定义两个缩放规则：

1. 如果 CPU 百分比高于 60%，则按 1 个实例进行扩大
2. 如果 CPU 百分比高于 85%，则按 3 个实例进行扩大

![多个缩放规则](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

启用这条附加规则后，如果在执行缩放操作之前负载超出 85%，会看到两个附加实例而不是一个。

## <a name="scale-based-on-a-schedule"></a>基于计划的缩放
默认情况下，当创建缩放规则时，它会始终应用。 可以在单击配置文件标头时看到该规则：

![配置文件](./media/insights-how-to-scale/Insights_Profile.png)

但是与在周末进行缩放相比，可能需要在一天或一周当中进行更积极的缩放。 甚至可以在工作时间之外完全关闭服务。

1. 为此，请在具有的配置文件中，选择“重复周期”而不是“总是”，并选择要应用配置文件的时间。
2. 例如，若要具有在一周中应用的配置文件，请在“天”下拉列表中取消选中“星期六”和“星期日”。
3. 要具有在白天应用的配置文件，请将“开始时间”设置为一天中希望开始进行的时间。
   
    ![默认重复周期](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)
4. 单击 **“确定”**。
5. 接下来，需要添加要在其他时间应用的配置文件。 单击“添加配置文件”行。
    ![下班](./media/insights-how-to-scale/Insights_ProfileOffWork.png)
6. 命名新的第二个配置文件，例如可将它称为“下班”。
7. 然后再次选择“重复周期”，然后选择此期间所需的实例计数范围。
8. 与默认配置文件一样，选择希望应用此配置文件的“天”，以及一天中的“开始时间”。
   
   > [!NOTE]
   > 自动缩放会对所选择的任何“时区”使用夏令时规则。 但是，在夏令时时间内，UTC 时差会显示基本时区时差，而不是夏令时 UTC 时差。
   > 
   > 
9. 单击 **“确定”**。
10. 现在，需要添加要在第二个配置文件实施期间应用的任何规则。 单击“添加规则”，并可以构建在默认配置文件实施期间具有的相同规则。
    
    ![向下班添加规则](./media/insights-how-to-scale/Insights_RuleOffWork.png)
11. 请务必为扩大和缩小都创建规则，否则在配置文件实施期间，实例计数只会增加（或减少）。
12. 最后，单击“保存”。

## <a name="next-steps"></a>后续步骤
* 
            [监视服务指标](insights-how-to-customize-monitoring.md)以确保服务可用且响应迅速。
* [启用监视和诊断](insights-how-to-use-diagnostics.md)以收集有关服务的详细高频率指标。
* 每当操作事件发生或指标超过阈值时[接收警报通知](insights-receive-alert-notifications.md)。
* 要确切了解代码在云中的执行情况时[监视应用程序性能](../application-insights/app-insights-azure-web-apps.md)。
* [查看事件和活动日志](insights-debugging-with-events.md)，以了解服务中发生的所有事件。
* 使用 Application Insights [监视任何网页的可用性和响应能力](../application-insights/app-insights-monitor-web-app-availability.md)，以便可以在页面出现故障时及时发现。


