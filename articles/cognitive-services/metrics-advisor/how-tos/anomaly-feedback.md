---
title: 向指标 Advisor 服务提供异常反馈
titleSuffix: Azure Cognitive Services
description: 了解如何在指标顾问实例发现的异常情况下发送反馈，并调整结果。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 11864cb26b76d414aa2efe2643797a2f66fa30e4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047601"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>使用反馈调整异常检测

如果你对指标监视器提供的某些异常检测结果不满意，则可以手动添加将影响应用于数据的模型的反馈。 

使用页面右上角的按钮激活反馈批注模式。

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="反馈批注模式。":::

激活反馈批注模式后，可以为一个点或多个连续点提供反馈。

## <a name="give-feedback-for-one-point"></a>为单点提供反馈 

激活反馈批注模式后，单击某个点以打开 " **添加反馈** " 面板。 您可以设置要应用的反馈类型。 此反馈将融入将来点的检测中。  

* 如果认为该点不正确地由指标监视器标记，请选择 " **异常** "。 可以指定某个点应为异常还是不应为异常。 
* 如果你认为该点指示趋势变化的开始，请选择 " **ChangePoint** "。
* 选择 " **时间段** " 以指示季节性。 指标监视器可以自动检测季节性的间隔，也可以手动指定此值。 

请考虑同时在 **注释** 文本框中保留注释，并单击 " **保存** " 以保存反馈。

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="反馈批注模式。":::

## <a name="give-feedback-for-multiple-continuous-points"></a>为多个连续点提供反馈

您可以通过单击鼠标并将鼠标拖动到要批注的点上，为多个连续点同时提供反馈。 你将看到与上面相同的反馈菜单。 单击 " **保存**" 后，将对所有选定的点应用相同的反馈。

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="反馈批注模式。":::

## <a name="how-to-view-my-feedback"></a>如何查看我的反馈

若要查看某个点的异常情况检测是否已更改，请将鼠标悬停在该点上。 工具提示将显示 **受反馈影响：** 如果检测已更改，则为 true。 如果显示 " **False**"，则反馈计算在点上完成，但异常检测结果未更改。

:::image type="content" source="../media/feedback/affected-point.png" alt-text="反馈批注模式。":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>何时应将异常批注为 "normal"

当你可能考虑异常情况时，有很多原因会导致误报。 如果满足以下任一情况，请考虑使用以下指标顾问功能：


|方案  |建议 |
|---------|---------|
|异常是由已知数据源更改（例如系统更改）引起的。     | 如果此方案不应定期发生，请不要批注此异常情况。        |
|异常是由假日导致的。     | 使用 [预设事件](configure-metrics.md#preset-events) 在指定时间标记异常检测。       |
|有一种定期模式检测到异常 (例如，) 周末，它们应该不是异常。      |使用反馈功能或预设事件。        |

## <a name="next-steps"></a>后续步骤
- [诊断事件](diagnose-incident.md)。
- [配置指标并微调检测配置](configure-metrics.md)
- [配置警报并使用挂钩获取通知](../how-tos/alerts.md)