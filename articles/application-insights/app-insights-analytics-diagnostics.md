---
title: "Azure Application Insights 中 Web 应用性能变化的智能诊断 | Microsoft Docs"
description: "自动诊断 Web 应用中性能遥测的峰值或步骤。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: cfreeman
ms.openlocfilehash: 5e53bc714d89bf6204681349e7890e0b8fbc7046
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>诊断应用遥测的突然变化

*此功能为预览版。*

只需单击即可诊断 Web 应用的性能或使用情况的突然变化！ 只要在 [Application Insights](app-insights-overview.md) 中的[分析](app-insights-analytics.md)中创建时间表，即可使用智能诊断功能。 无论结果趋势中出现任何异常变化（如峰值或谷值），智能诊断都会识别出其维度和相关值的模式，尝试对变化做出解释。 这样有助于快速诊断问题。 

在此示例中，智能诊断识别出了与变化相关的属性值的模式，并突出显示了使用和不使用该模式的结果之间的差异：

![分析诊断结果示例](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>诊断数据变化

1.  在分析中运行查询，并以时间表的形式进行呈现。 
2.  单击任何突出显示的峰值点（如有）。
 
    ![峰值点](./media/app-insights-analytics-diagnostics/peak.png)

    诊断需要几秒钟来找到模式。

3. “诊断结果”选项卡中显示了一个模式，该模式也许可以解释数据中断的原因。

    ![结果](./media/app-insights-analytics-diagnostics/result.png)
 
    文本显示了可能与变化相关的维度值。 在此示例中，它与特定请求和特定浏览器版本有关。

    另外请注意图表中可以筛选 true 和 false 的两个组件。 false 组件显示了未发生变化的趋势。 换而言之，如果排除了诊断识别出的一系列维度问题，遥测结果不会发生变化。 与此相反，如果包含这些问题，则结果会在突出显示的调查区域内显示出巨大变化。 这表明诊断已经找到了可以解释变化的一系列属性。

4.  如果模式很复杂，则需要将鼠标悬停在“全部显示”上以查看维度。

    ![全部显示](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.  如果诊断没有发现可通知的明显模式，则会显示“无结果”页面。 在这种情况下，建议更改查询方式。 例如，可以缩小分析查询中的时间范围和划分，以进行深入分析，这样也许可以获得更好的结果。

弄清楚网站的哪个页面在哪个浏览器上存在问题后，就可以直接转到问题页面，并调查最近做出的更改。

## <a name="try-the-demo"></a>尝试演示

[单击此处查看示例数据演示](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H)。

## <a name="how-it-works"></a>工作原理

智能诊断使用了先进的基于 [DiffPatterns](app-insights-analytics-reference.md) 操作的非监督式机器学习算法。 该算法会查找也许可以解释数据变化的候选模式。 它会分析每个候选模式对指标的影响，并显示与变化最相关的模式。

## <a name="no-diagnostic-points"></a>没有诊断点？

仅在满足以下条件时，智能诊断才适用：

 * 智能诊断设置已打开。 请在分析中的“设置”图标下查看。
 * 已在分析设置中选中智能诊断选项。 
 * 时间轴：图表的 X 轴必须为 `datetime` 类型。
 * 折线图或分区图：诊断仅适用于此类图表。 在查询末尾使用 `| render timechart` 或 `| render areachart`；或从下拉选择器中选择折线图或分区图。
 * 中断：数据中必须存在明显中断。
 * 用于分析的点要足够多。
 * 查询中的汇总子句不能超过一个。
 * 汇总子句之前不存在包含名称定义的项目子句。

 
 ## <a name="related-articles"></a>相关文章

 * [Analytics 教程](app-insights-analytics-tour.md)
 * 遇到性能问题时，[智能检测](app-insights-proactive-diagnostics.md)会自动发出警报。