---
title: 使用 web 门户配置指标顾问实例
titleSuffix: Azure Cognitive Services
description: 如何配置指标顾问实例并精细调整异常情况检测结果。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 7923dad3d47122c0ceb04d1240736e2b66a0dd64
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048247"
---
# <a name="how-to-configure-metrics-and-fine-tune-detecting-configuration"></a>如何：配置度量值和微调检测配置

使用本文开始使用 web 门户配置指标顾问实例。 若要浏览特定数据馈送的指标，请转到 " **数据馈送** " 页，然后选择其中一个源。 这将显示与之关联的指标列表。

:::image type="content" source="../media/metrics/select-metric.png" alt-text="选择指标" lightbox="../media/metrics/select-metric.png":::

单击其中一个指标名称可查看其详细信息。 在此详细视图中，你可以使用屏幕右上角的下拉列表切换到同一数据馈送中的其他指标。

第一次查看度量值的详细信息时，可以通过让度量顾问选择一个时间序列，或指定要为每个维度包含的值来加载时序。 

还可以选择时间范围，并更改页面的布局。

> [!NOTE]
> - 开始时间为包含时间。
> - 结束时间为排他。 

你可以单击 " **事件** " 选项卡以查看异常，并找到指向 [事件中心](diagnose-incident.md)的链接。

## <a name="tune-the-detecting-configuration"></a>优化检测配置

指标可以应用一个或多个检测配置。 每个指标都有一个默认配置，你可以根据监视需要对其进行编辑或添加。

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>优化当前指标中所有序列的配置

此配置将应用于此指标中的所有系列，但具有单独配置的系列除外。 默认情况下，在载入数据时应用指标级别配置，并显示在左侧面板上。 用户可以直接在 "指标" 页上编辑指标级别配置。 

还有其他参数（如 **方向**）和可用于进一步优化配置的 **有效异常** 。 也可以组合不同的检测方法。 

:::image type="content" source="../media/configuration-combination.png" alt-text="选择指标" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>优化特定系列或组的配置

单击 "指标级别" 配置选项下方的 " **高级配置** "，查看组级别配置。您可以通过单击此窗口中的图标为单个序列或一组序列添加配置 **+** 。 参数与指标级别的配置参数类似，但你可能需要为组级别的配置至少指定一个维度值才能标识一组序列。 并指定系列级别配置的所有维度值以标识特定系列。 

此配置将应用于系列组或特定系列，而不是指标级别配置。 设置此组的条件后，将其保存。

:::image type="content" source="../media/advanced-configuration.png" alt-text="选择指标" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>异常情况检测方法

指标顾问提供多种异常情况检测方法。 可以通过单击按钮使用逻辑运算符或将其与逻辑运算符组合使用 **+** 。 

**智能检测** 

智能检测由机器学习提供支持，可了解历史数据的模式，并将其用于将来的检测。 使用此方法时， **敏感度** 是用于优化检测结果的最重要的参数。 可以将其拖动到更小或更大的值，以影响页面右侧的可视化效果。 选择一个适合你的数据的并保存。 


在智能检测模式下，敏感度和边界版本参数用于微调异常情况检测结果。

敏感度可能会影响每个点的预期值范围的宽度。 增加时，预期的值范围将更紧密，并报告更多异常：

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="选择指标":::

关闭敏感度后，预期的值范围将更宽，并且报告的异常将更少：

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="选择指标" 作为异常情况检测方法。
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="选择指标":::

2. 根据方案选择 **超出范围** 或 **范围** 参数。

    如果要检测波动，请选择 **"超出范围"**。 例如，使用下面的设置时，与前一项相比，更改10% 以上的任何数据点将被检测为离群项。
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="选择指标":::

    如果要检测数据中的平行，请 **在范围**内选择。 例如，使用以下设置时，0.01% 内发生变化的任何数据点将被检测为离群项。 由于阈值太小 (0.01% ) ，它将数据中的平面行检测为离群值。

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="选择指标":::

3. 设置将计为异常的更改的百分比，并将以前捕获的数据点用于比较。 此比较总是在当前数据点和它之前的单个数据点 N 点之间进行。
    
    仅当使用**超出范围**模式时，**方向**才有效：
    
    * 将**检测配置为**仅在 (当前数据点)  (比较数据点) > 阈值百分比时检测异常 **+** 。
    * 将**检测配置为**仅在 (当前数据点)  (比较数据点) < 阈值百分比时检测异常 **-** 。
 
**硬阈值**

 硬阈值是异常检测的基本方法。 可以设置上限和/或下限来确定预期的值范围。 边界外的任何点将被标识为异常。 


## <a name="preset-events"></a>预设事件

有时，预期的事件和出现 (例如假期) 会生成异常数据。 使用预设事件，可以在指定的时间将标志添加到异常情况检测输出。 在载入数据馈送后，应配置此功能。 每个度量值只能有一个预设的事件配置。

> [!Note]
> 预设事件配置将在异常检测期间考虑假日，并可能会更改结果。 在保存配置后，它将应用于数据点引入。 

单击每个指标详细信息页上 "指标" 下拉列表旁边的 " **配置预设事件** " 按钮。
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="选择指标" 部分可帮助你取消在假日期间检测到的不必要的异常。 可以应用 **策略** 选项的两个选项：

* **取消假日**：在假日期间取消异常检测结果中的所有异常和警报。
* **假日作为周末**：计算节假日前几个对应周末的平均预期值，并将异常状态从这些值中脱离。

你还可以配置一些其他值：

|选项  |说明  |
|---------|---------|
|**选择一个维度作为国家/地区**     | 选择包含国家/地区信息的维度。 例如，国家/地区代码。         |
|**国家/地区代码映射**     | 标准 [国家/地区代码](https://wikipedia.org/wiki/ISO_3166-1_alpha-2)与所选维度的国家/地区数据之间的映射。        |
|**假日选项**    | 无论是要考虑所有假日，只需 (PTO) 假日，还是仅假日，或只使用非 PTO 假期。         |
|**扩展天数**    |  假期前后受影响的天。        |


在某些情况下，可以使用 **Cycle 事件** 部分，通过使用数据中的循环模式来帮助减少不必要的警报。 例如： 

- 具有多个模式或循环的指标，如每周和每月模式。 
- 不具有清晰模式的指标，但数据可与年份相差 (同比变化率) ，月， (MoM) ，Week 周 (WoW) ，或每天 (DoD) 。
 
并非所有选项都可用于每个粒度。 每个粒度的可用选项如下所示：

| 粒度 | 同比变化率 | MoM | 哎呀 | DoD |
|:-|:-|:-|:-|:-|
| 每年 | X | X | X | X |
| 每月一次 | X | X | X | X |
| 每周 | ✔ | X | X | X |
| 每天 | ✔ | ✔ | ✔ | X |
| 每小时 | ✔ | ✔ | ✔ | ✔ |
| Minutely | X | X | X | X |
| 第 | X | X | X | X |
| 客户 | ✔ | ✔ | ✔ | ✔ |

X-不可用。  
✔。
  
\* 如果使用自定义粒度（以秒为单位），则仅当度量值超过一小时且小于一天时才可用。

循环事件用于在遵循循环模式时减少异常情况，但如果多个数据点不遵循模式，则将报告异常。 如果一个数据点不遵循该模式，则**严格模式**用于启用异常报告。 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="选择指标":::

## <a name="view-recent-incidents"></a>查看最近事件

指标顾问在引入的所有时序数据中检测异常。 但是，并不是所有异常都需要升级，因为它们可能不会产生很大的影响。 聚合将在出现异常时执行，以将相关事件分组到事件中。 可以在 "指标详细信息" 页的 " **事件** " 选项卡中查看这些事件。 

单击事件可访问 " **事件分析** " 页，可在其中查看有关此事件的更多详细信息。 单击 " **管理新事件中心的事件**"，以查找 " [事件中心](diagnose-incident.md) " 页，您可以在其中找到特定指标下的所有事件。 

## <a name="subscribe-anomalies-for-notification"></a>订阅异常通知

如果希望每当检测到异常时收到通知，则可以使用挂钩订阅指标的警报。 有关详细信息，请参阅 [配置警报和使用挂钩获取通知](alerts.md) 。


## <a name="next-steps"></a>后续步骤 
- [配置警报并使用挂钩获取通知](alerts.md)
- [使用反馈调整异常检测](anomaly-feedback.md)
- [诊断事件](diagnose-incident.md)。

