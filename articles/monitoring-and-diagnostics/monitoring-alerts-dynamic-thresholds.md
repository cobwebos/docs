---
title: 在 Azure Monitor 中创建具有动态阈值的警报 | Microsoft Docs
description: 使用基于机器学习的动态阈值创建警报
author: antonfrMSFT
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: antonfr;mbullwin
ms.openlocfilehash: 45d2f39ff7fb2e5a22cda74cd0c794db28e351f1
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33943291"
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-public-preview"></a>Azure Monitor 中具有动态阈值的警报（受限公开预览版）

具有动态阈值的警报是 Azure Monitor 中 Azure 指标警报的增强功能，它利用先进的机器学习 (ML) 功能来学习指标的历史行为，以自动计算基线并将它们用作警报阈值。

使用动态阈值的好处如下：

- 不必费心设置预定义的刚性边界，因为 Monitor 会自动学习指标的历史性能，并应用 ML 算法来确定警报阈值。
- 它们可以识别季节性行为，并且仅在偏离预期的季节性行为时发出警报。 如果你的服务经常在周末闲置，然后在每个星期一达到峰值，则不会触发具有动态阈值的指标警报。 目前支持：每小时、每天和每周的季节性。
- 不断学习指标性能，并适应指标的变化。

基于动态阈值的警报适用于此[文章](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for)中列出的所有基于 Azure Monitor 的指标源。

## <a name="sign-up-to-access-the-preview"></a>注册以访问预览版

若要先行试用此功能，[请注册预览版](http://aka.ms/DynamicThresholdMetricAlerts)。 与往常一样，我们很乐意听取你的反馈意见，请将其发送到 [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>如何配置具有动态阈值的警报

可以通过 Azure Monitor 中的“警报”配置具有动态阈值的警报

![警报预览版](./media/monitoring-alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>创建具有动态阈值的警报规则

1. 从“Monitor”下的“警报”窗格中，选择“新建警报规则”按钮以在 Azure 中新建警报。

   ![新建警报规则](./media/monitoring-alerts-dynamic-thresholds/002.png)

2. 此时会显示“创建规则”部分，其中包括三个组成部分：_定义警报条件_、_定义警报详细信息_和_定义操作组_。 先从_定义警报条件_部分开始，使用“选择目标”链接通过选择资源来指定目标。 一旦选择了合适的资源，就单击“完成”按钮。

   ![选择目标](./media/monitoring-alerts-dynamic-thresholds/0003.png)

3. 接下来，使用“添加条件”按钮查看适用于该资源的信号选项列表，并从信号列表中选择合适的**指标**选项。 （例如 CPU 百分比）。

   ![添加条件](./media/monitoring-alerts-dynamic-thresholds/004.png)

4. 在“配置信号逻辑”屏幕上的“警报逻辑”部分，可以选择将条件切换为“动态”类型，从而自动在指标（蓝线）旁边生成动态阈值（红线）。

   ![动态](./media/monitoring-alerts-dynamic-thresholds/005.png)

5. 图表中显示的阈值是根据过去七天的历史数据计算得到的，一旦创建警报，动态阈值就会获取更多可用的历史数据，并根据新数据不断学习，使自身更加准确。

6. 其他警报逻辑设置：
   - 条件 - 可以选择要在符合以下三种条件之一时触发的警报：
       - 大于阈值上限或低于阈值下限（默认条件）
       - 大于阈值上限
       - 低于阈值下限。
   - 时间聚合：平均（默认值）、总和、最大最小值。
   - 警报敏感度：
       - 高 - 较多警报，因为警报将在偏差最小时触发。
       - 中 - 相比高敏感度，敏感度更低，警报数量更少（默认值）
       - 低 - 最不敏感的阈值。

    ![警报逻辑设置](./media/monitoring-alerts-dynamic-thresholds/00007.png)

7. 计算基于：
    -  持续时间，警报应通过从“时间段”中进行选择来查找指定条件。

    ![计算基于](./media/monitoring-alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > 受支持的时间段值：5 分钟、10 分钟、30 分钟和 1 小时。

   为了减少临时峰值产生的警报噪音，建议使用“触发警报的违反次数”设置。 使用此功能时，只有在连续 X 次违反阈值或在过去 Z 个时间段内违反阈值 Y 次时才收到警报。 例如：

    若要在问题持续 15 分钟（给定时间段为 5 分钟时连续 3 次）时触发警报，请使用以下设置：

   ![计算基于](./media/monitoring-alerts-dynamic-thresholds/0008.png)

    若要在过去 30 分钟（时间段为 5 分钟）内有 15 分钟出现违反动态阈值的情况下触发警报，请使用以下设置：

   ![计算基于](./media/monitoring-alerts-dynamic-thresholds/0009.png)

8. 目前，用户可以将警报的动态阈值条件作为单一条件。

   ![创建规则](./media/monitoring-alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>问题解答

- 问：如果指标随时间逐渐变化，是否会触发具有动态阈值的警报？

- 答：可能不会。 动态阈值适合检测重大偏差，而不适合检测逐渐形成的问题。

- 问：能否通过 API 配置动态阈值？

- 答：我们正努力开发此功能。
