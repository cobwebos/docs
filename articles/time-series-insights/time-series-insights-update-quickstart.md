---
title: 浏览 Azure 时序见解（预览版）演示环境 | Microsoft Docs
description: 了解 Azure 时序见解（预览版）演示环境
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 12/03/2018
ms.openlocfilehash: 76bdc122cab5b4ee87717c9c4df9774ecc3bd8f7
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888768"
---
# <a name="explore-the-azure-time-series-insights-preview-demo-environment"></a>浏览 Azure 时序见解（预览版）演示环境

本快速入门说明如何在免费的演示环境中开始使用 Azure 时序见解 (TSI) 预览版资源管理器。 了解如何使用 Web 浏览器将大量历史性的行业 IoT 数据可视化，并浏览 Azure 时序见解（预览版）资源管理器的关键功能。

Azure TSI 是端到端平台即服务套餐，用于引入、处理、存储和查询高度情景化、针对时序优化的 IoT 规模的数据，以便进行即席数据浏览和运营分析。 时序见解是一项差异化的套餐，满足行业 IoT 部署的独特需求。

演示环境演示了发电公司 Contoso，该公司使用 TSI 在其数据中查找可行的见解并进行时间很短的根本原因分析。 Contoso 运营两个风力涡轮机发电厂，每个厂有 10 个涡轮机，每个涡轮机有 20 个传感器，每隔一分钟将数据报告给 Azure IoT 中心。 传感器收集的信息涉及天气条件、叶片间距和偏离位置、发电机性能、变速箱行为以及安全监视器。

TSI（预览版）用于分析自过去两年以来不断增长的数据集 – 目前为 40 GB – 目的是更好地了解和预测严重故障和慢慢出现的维护问题。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费 Azure 帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 。

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>在演示环境中浏览时序见解资源管理器

1. 在浏览器中，导航到  [insights.timeseries.azure.com/preview/samples](https://insights.timeseries.azure.com/preview/samples)。  

1. 如果系统提示，请使用 Azure 帐户凭据登录到 TSI 资源管理器。

### <a name="demo-step-one"></a>演示步骤一

1. 让我们看看**发电厂 1 中的风力涡轮机 7**。  

    * **操作**：将视图范围更新为 `1/1/17 20:00 – 3/10/17 20:00 (UTC)` 并添加 `Farm 1 > W7 > Generator > GeneratorSpeed` 传感器。 然后，显示生成的值。

       ![快速入门一][1]

1. 最近，**Contoso 发现风力涡轮机 7 起火**。 让我们从这里开始了解。 我们可以看到火警传感器在起火期间激活。

    * **操作**：将视图范围更新为 `3/9/17 20:00 – 3/10/17 20:00 (UTC)` 并添加 `Safety > FireAlert` 传感器。

      ![快速入门二][2]

1. 让我们看看在起火时还发生了什么。 油压和活动警告刚好在起火前激发，但这时已为时过晚，无法避免问题的发生。

    * **操作**：添加 `Pitch > HydraulicOilPressure` 传感器和 `Pitch > ActiveWarning` 传感器。

      ![快速入门三][3]

1. 如果缩小范围，可以看到在起火前有预兆。 两个传感器都出现了波动。 那么，这种情况以前是否发生过？

    * **操作**：将视图范围更新为 `2/24/17 20:00 – 3/10/17 20:00 (UTC)`。

      ![快速入门四][4]

1. 如果我们检查这两年的所有数据，可以发现以前的起火事件也有相同的征兆。 我们可以使用该数据生成相应的系统，以便及早捕获此类问题。

    * **操作**：将视图范围更新为 `1/1/16 – 12/31/17`（所有数据）。

       ![快速入门五][5]

### <a name="demo-step-two"></a>演示步骤二

1. 其他问题更加微妙且更难以诊断。 时序见解提供了一系列功能，帮助我们跟踪棘手的问题。 在这里，我们可以看到警告传感器在 `6/25` 的`turbine #6` 上出现中断。 但是，到底发生了什么？

    * **操作**：删除当前传感器。 然后，将视图范围更新为 `6/1/17 20:00 – 7/1/17 20:00 (UTC)` 并添加 `Farm 1 > W6 > Safety > VoltageActuatorSwitchWarning`。

       ![快速入门六][6]

1. 警告指示发电机输出的电压出现问题。 但是，问题原因是什么？ 从较小的时间间隔来看，发电机的总体电力输出正常。 但如果将数据聚合起来，就可以发现明确的下降。

    * **操作**：删除 `VoltageActuatorSwitchWarning` 并添加 `Generator > ActivePower`，然后将时间间隔更新为 `3d`。

       ![快速入门七][7]

1. 如果我们在数据集中向前导航，则可以看到这不仅仅是一个暂时性问题。 问题在继续。

    * **操作**：将时间跨度向右展开。

       ![快速入门八][8]

1. 让我们进一步深入探讨问题。 我们可以使用其他传感器数据点按相来查看电压。 但是，它们看起来都差不多。 让我们放一个标记，以便查看实际值。 看起来 3 相输出存在问题。

    * **操作**：`Add Generator > GridVoltagePhase1, 2, & 3`。 在可见区域的最后一个数据点放一个标记。

       ![快速入门八][8]

1. 如果我们使用同样的比例来查看所有三项数据，可以看到 3 相的下降趋势更明显。 现在我们了解了产生警告的原因，可以将此问题转给维护团队去处理了。  

    * **操作**：更新显示，将所有传感器按同一图表比例叠加显示。

       ![快速入门九][9]

## <a name="next-steps"></a>后续步骤

可以创建自己的 Azure TSI（预览版）环境了：

> [!div class="nextstepaction"]
> [规划 Azure TSI（预览版）环境](time-series-insights-update-plan.md)

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