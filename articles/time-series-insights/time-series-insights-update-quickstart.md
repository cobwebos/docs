---
title: 快速入门：浏览 Azure 时序见解预览版演示环境 | Microsoft Docs
description: 了解 Azure 时序见解预览版演示环境。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276819"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>快速入门：浏览 Azure 时序见解预览版演示环境

本快速入门说明如何在免费的演示环境中使用 Azure 时序见解预览版资源管理器。 了解如何使用 Web 浏览器将大量历史性的行业 IoT 数据可视化，并浏览时序见解预览版资源管理器的关键功能。

时序见解提供端到端的平台即服务 (PaaS) 产品/服务。 它可以为临时数据浏览引入、处理和查询高度情景化且时序优化的 IoT 规模的数据。 还提供运营分析。 时序见解是一项差异化的产品/服务，满足行业 IoT 部署的独特需求。

演示环境展示了 Contoso 发电公司的情况。 在环境中，可以使用时序见解在 Contoso 公司的数据中查找可操作的见解，并进行简短的根本原因分析。 Contoso 公司经营着两个风电场，每个风电场都有 10 个涡轮机。 每台涡轮机都有 20 个传感器，这些传感器每隔一分钟向 Azure IoT 中心报告数据。 传感器收集的信息涉及天气条件、叶片间距和偏离位置、发电机性能、变速箱行为以及安全监视器。

使用时序见解预览版分析过去两年中 Contoso 公司不断增长的数据集，当前已达到 40 GB。 这有助于更好地了解和预测严重故障和缓慢变化的维护问题。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费 Azure 帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 。

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>在演示环境中浏览时序见解资源管理器

1. 在浏览器中，请转到 [Contoso 风电场环境](https://insights.timeseries.azure.com/preview/samples)。  

1. 如果系统提示，请使用 Azure 帐户凭据登录到时序见解资源管理器。

### <a name="demo-step-1"></a>演示步骤 1

1. 让我们看看“Contoso 发电厂 1”中的风力涡轮机“W7”。  

    * **操作**：将视图范围更新为“17/1/1 20:00 至 17/3/10 20:00 (UTC)”，添加“Contoso 发电厂 1” > “W7” > “发电机系统” > “GeneratorSpeed”传感器，然后显示结果值。

       ![快速入门一][1]

1. 最近，Contoso 公司发现风力涡轮机 W7 起火。 让我们从这里开始了解。 我们可以看到火警传感器在起火期间激活。

    * **操作**：将视图范围更新为“17/3/9 20:00 至 17/3/10 20:00 (UTC)”，并添加“安全系统” > “FireAlert”传感器。

      ![快速入门二][2]

1. 让我们看看在起火时还发生了什么。 油压和活动警告刚好在起火前激发，但这时已为时过晚，无法避免问题的发生。

    * **操作**：添加“间距系统” > “HydraulicOilPressure”传感器和“间距系统” > “ActiveWarning”传感器。

      ![快速入门三][3]

1. 如果缩小范围，可以看到在起火前有预兆。 两个传感器都出现了波动。 那么，此问题以前是否发生过？

    * **操作**：将视图范围更新为“17/2/24 20:00 至 17/3/10 20:00 (UTC)”。

      ![快速入门四][4]

1. 如果我们检查这两年的所有数据，可以发现以前的起火事件也有相同的征兆。 我们可以使用该数据生成相应的系统，以便及早捕获此类问题。

    * **操作**：将视图范围更新为“16/1/1 至 17/12/31”（所有数据）。

       ![快速入门五][5]

### <a name="demo-step-2"></a>演示步骤 2

1. 其他问题更加微妙且更难以诊断。 时序见解提供了各种方法来帮助我们跟踪棘手的问题。 在这里，我们可以看到在 6 月 25 日 W6 上的警告传感器出现中断。 但是，到底发生了什么？

    * **操作**：删除当前传感器，将视图范围更新为“17/6/1 20:00 至 17/7/1 20:00 (UTC)”，然后添加“Contoso 发电厂 1” > “W6” > “安全系统” > “VoltageActuatorSwitchWarning”传感器。

       ![快速入门六][6]

1. 警告指示发电机输出的电压出现问题。 但是，问题原因是什么？ 从较小的时间间隔来看，发电机的总体电力输出正常。 但通过汇总数据，我们可以发现明确的下降趋势。

    * **操作**：删除“VoltageActuatorSwitchWarning”传感器，添加“发电机系统” > “ActivePower”传感器，然后将间隔时间更新为“3 天”。

       ![快速入门七][7]

1. 如果我们在数据集中向前导航，则可以看到这不是暂时性问题。 它是一个连续性的问题。

    * **操作**：将时间跨度向右展开。

       ![快速入门八][8]

1. 让我们进一步深入探讨问题。 我们可以添加其他传感器数据点按阶段查看电压。 但所有数据点看起来都类似。 让我们放一个标记，以便查看实际值。 看起来第 3 阶段的输出存在问题。

    * **操作**：添加“发电机系统” > “GridVoltagePhase1”、“GridVoltagePhase2”和“GridVoltagePhase3”传感器。 在可见区域的最后一个数据点放一个标记。

       ![快速入门八][8]

1. 如果我们使用同样的比例来查看所有三个数据点，可以看到第 3 阶段的下降趋势更加明显。 现在我们了解了产生警告的原因，可以将此问题转给维护团队去处理了。  

    * **操作**：更新显示，将所有传感器按同一图表比例叠加显示。

       ![快速入门九][9]

## <a name="next-steps"></a>后续步骤

已准备好创建自己的时序见解预览版环境：

> [!div class="nextstepaction"]
> [计划时序见解预览版环境](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
