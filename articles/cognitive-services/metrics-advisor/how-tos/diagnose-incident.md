---
title: 使用指标顾问诊断事件
titleSuffix: Azure Cognitive Services
description: 了解如何使用指标顾问诊断事件，并获取数据中异常的详细视图。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: d1f792859aa5407cfaceda0e3ed1b5c21ee160f0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043368"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>如何使用指标顾问诊断事件

指标顾问提供多种诊断功能，并提供检测到的事件的详细视图，并提供根本原因分析。 当在指标上检测到一组异常时，指标顾问会将异常分组到一个层次结构中并在其上进行分析。

> [!NOTE]
> 目前，指标顾问支持至少具有一个维度的指标的事件诊断，并通过  *数值* 类型进行度量。 对于用于构建诊断层次结构的每个维度，指标需要具有聚合维度值（如 SUM）。 指标顾问提供 [**自动汇总设置**](onboard-your-data.md#automatic-roll-up-settings) 来帮助生成聚合值。 

单击左侧导航窗口中的 " **事件中心** "，查看给定指标下的所有事件。 在页面顶部，可以选择不同的指标来查看其检测配置和检测结果，并更改时间范围。

> [!TIP]
> 还可以通过以下方式访问 **事件中心** ：
> * 单击度量值的可视化效果中的数据点，并使用显示的 " **添加反馈** " 窗口底部的链接。
> * 在指标的 " **事件** " 选项卡中单击其中一个异常。 

" **概述** " 部分包含检测结果，其中包括所选时间范围内的异常和警报的计数。

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="事件中心" lightbox="../media/diagnostics/incident-hub-overview.png":::

**事件列表**中列出了所选度量值和时间范围内的事件。 有一些选项可用于筛选和排序事件。 例如，按严重性。 单击其中一个事件以切换到 " **事件** " 页，进一步诊断。

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="事件中心" lightbox="../media/diagnostics/incident-list.png":::

通过 " **诊断** " 部分，你可以对事件执行深入分析，并使用工具来确定根本原因。

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="事件中心" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>根本原因建议

当在某个指标检测到一组异常并导致事件时，指标顾问将尝试分析该事件的根本原因。 **根本原因建议** 为事件的可能原因提供自动建议。 仅当维度中存在聚合值时，此功能才可用。 如果度量值没有维度，根本原因将是本身。 根原因列在右侧面板中，可能有几个原因列出。 如果表中没有数据，则意味着维度不满足执行分析的要求。

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="事件中心" 以查看指标的更多详细信息。

## <a name="incident-tree"></a>事件树

除了自动分析可能的根本原因，指标顾问还使用 **事件树**支持手动根本原因分析。 "事件" 页中有两种类型的事件树： **快速诊断** 树和 **交互式树**。

快速诊断树用于诊断当前事件，根节点限制为当前事件根节点。 您可以通过单击树节点来展开和折叠它们的序列，并将其系列与树上方的图表中的当前事件系列一起显示。

交互式树允许您诊断当前事件、旧事件和相关事件。 使用交互式树时，右键单击某个节点可打开 "操作" 菜单，您可以在该菜单中选择一个维度以向上钻取根节点，并使用一个维度向下钻取到每个节点。 通过单击顶部维度列表的 "取消" 按钮，可以从该维度中删除钻取或下移。 左键单击某个节点，将其选中，并在图表中显示与当前事件系列一起显示的序列。

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="事件中心" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>异常向下钻取

查看事件信息时，可能需要获取更详细的信息，例如，针对不同的维度和时间戳。 如果数据有一个或多个维度，则可以使用深化函数获取更详细的视图。 

若要使用向下钻取功能，请单击**事件中心**中的 "**指标钻取**" 选项卡。 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="事件中心" 设置是事件的维度列表，您可以为每个维度选择其他可用维度值。 更改维度值后。 利用 **时间戳** 设置，你可以在不同时刻查看当前事件。

### <a name="select-drilling-options-and-choose-a-dimension"></a>选择钻取选项并选择维度

深化选项分为两种类型： **向下钻取** 和 **水平比较**。

> [!Note]
> 1. 对于向下钻取，您可以从不同的维度值（当前所选维度除外）探索数据。 
> 2. 对于水平比较，您可以从不同的维度值中浏览数据，这些数据与所有维度不同。

:::image type="content" source="../media/diagnostics/drill-down-dimension.png"  lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="事件中心":::

### <a name="value-comparison-for-different-dimension-values"></a>不同维度值的值比较

"向下钻取" 选项卡的第二部分是一个表，其中包含不同维度值的比较结果。 它包括值、基线值、差异值、增量值以及是否为异常。
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="事件中心" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>不同维度值的值和预期值比较

"深化" 选项卡的第三部分是一个直方图，其中包含值和预期值，适用于不同的维度值。 直方图按值和预期值之间的差异进行排序。 您可以轻松地找到意外的值。 例如，在上图中，我们可以发现除所有值外， **US7** 最适用于异常情况。

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="事件中心" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>原始值可视化
"向下钻取" 选项卡的最后一部分是原始值的折线图。 提供此图表后，无需导航到 "指标" 页即可查看详细信息。

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="事件中心" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>使用时序分析查看类似的异常

查看事件时，可以使用类似的 "时序 **群集** " 选项卡查看与其关联的各个序列。 一组中的系列汇总在一起。 从上图中，我们可以知道至少有两个序列组。 仅当满足以下要求时，此功能才可用：

1. 度量值必须具有一个或多个维度或维度值。
2. 一个指标内的序列必须具有类似的趋势。

可用维度在选项卡的顶部列出，您可以选择指定序列。

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png"alt-text="事件中心":::

## <a name="compare-time-series"></a>比较时序

有时，如果在特定时序上检测到异常，则在单个可视化效果中将其与多个其他序列进行比较会很有帮助。 单击 " **比较工具** " 选项卡，然后单击蓝色的 " **+ 添加** " 按钮。 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="事件中心" lightbox="../media/diagnostics/add-series.png":::

从数据馈送中选择一个序列。 您可以选择相同的粒度或不同的粒度。 选择目标维度并加载序列趋势，并单击 **"确定"** 以将其与前一个序列进行比较。 序列将在一个可视化效果中组合在一起。 您可以继续添加更多系列以进行比较并获得更多见解。 单击 " **比较工具** " 选项卡顶部的下拉菜单，比较时间段内的时序数据。  

> [!Warning]
> 若要进行比较，时序数据分析可能需要移动数据点，以便数据的粒度必须支持。 例如，如果你的数据是每周的，并且你使用 **的是日比较，** 则不会产生任何结果。 在此示例中，你将改为使用 **月的比较** 。

选择时间增量比较后，您可以选择是要比较数据值、增量值还是百分比增量。

> [!Note]
> * **数据值** 为原始数据值。
> * **增量值** 是原始值和比较值之间的差异。
> * **百分比 delta 值** 是原始值和比较值与比较值之间的差异。

## <a name="related-incidents-across-metrics"></a>跨指标的相关事件

有时，可能需要同时检查不同指标的事件或其他指标中的相关事件。 可以在 " **跨指标分析** " 部分找到相关事件的列表。 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="事件中心" 添加关系。 只有具有相同维度名称的指标才能相关。 使用以下参数。

- 当前数据馈送 & 指标：当前事件的数据馈送和指标
- 方向：两个度量值之间的关系方向。  (立即对相关事件列表无效) 
- 其他数据馈送 & 指标：要与当前指标连接的数据馈送和指标


## <a name="next-steps"></a>后续步骤 

- [使用反馈调整异常检测](anomaly-feedback.md)
- [配置指标并微调检测配置](configure-metrics.md)
