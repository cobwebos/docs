---
title: Azure Monitor 指标资源管理器
description: 了解 Azure Monitor 指标资源管理器中的新功能
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 576a7948d4df7957d934fb4c8888712fa94cb202
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337068"
---
# <a name="azure-monitor-metrics-explorer"></a>Azure Monitor 指标资源管理器

Azure Monitor 指标资源管理器是 Microsoft Azure 门户的一个组件，通过它可以绘制图表、以直观的形式关联趋势及研究指标值的上升和下降。 指标资源管理器是研究 Azure 托管的或 Azure Monitor 服务监视的应用程序，以及基础结构存在的各种性能问题和可用性问题的基本起始点。

## <a name="metrics-in-azure"></a>Azure 中的指标

[Azure Monitor 中的指标](data-collection.md#metrics)是随着时间的推移收集和存储的一系列测量值和计数。 有标准（或“平台”）指标和自定义指标。 标准指标由 Azure 平台本身提供。 标准指标反映 Azure 资源的运行状况和使用情况统计信息。 而由应用程序通过发送到 Azure 的自定义指标[自定义事件和指标的 Application Insights API](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics)， [Windows Azure 诊断 (WAD) 扩展](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)，或由[Azure监视 REST API](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api)。

## <a name="create-a-new-chart"></a>创建新图表

1. 打开 Azure 门户
2. 导航到新的“监视”选项卡，并选择“指标”。

   ![指标图像](./media/metrics-charts/00001.png)

3. “指标选择器”将自动打开。 从列表中选择一个资源，查看它关联的指标。 列表将只显示具有指标的资源。

   ![指标图像](./media/metrics-charts/00002.png)

   > [!NOTE]
   >如果有多个 Azure 订阅，指标资源管理器将拉取“门户设置 -> 按订阅筛选”列表中选定的所有订阅中的资源。 若要更改此设置，请单击屏幕顶部的“门户设置”齿轮图标，然后选择要使用的订阅。

4. 对于某些资源类型（即存储帐户和虚拟机），选择指标前必须先选择**命名空间**。 每个命名空间都带有自己的指标集，这些指标只与此命名空间有关，而与其他命名空间无关。

   例如，每个 Azure 存储空间都包含针对次级服务“Blob”、“文件”、“队列”和“表”（这些均是存储帐户的组成部分）的指标。 但是，指标“队列消息计数”必然只适用于次级服务“队列”，而不适用于任何其他存储帐户次级服务。

   ![指标图像](./media/metrics-charts/00003.png)

5. 从列表中选择一个指标。 如果知道所需指标的部分名称，可以将其键入，即可看到可用指标的筛选列表：

   ![指标图像](./media/metrics-charts/00004.png)

6. 选择指标后，将显示图表，其中包含对所选指标的默认聚合。 此时，在“指标选择器”之外单击即可将其关闭。 也可以选择将图表切换到不同的聚合。 对于一些指标，可以通过切换聚合选择想要在图表上看到的值。 例如，可以在平均值、最小值和最大值之间切换。 

7. 通过单击“添加指标”并重复步骤 3-6，你可以在同一个图表上添加其他指标。

   > [!NOTE]
   > 通常情况下，你不会想要在一个图表上拥有度量单位不同（即“毫秒”和“千字节”）或刻度差异显著的多个指标。 此时，可考虑使用多个图表。 单击“添加图表”按钮，即可在指标资源管理器中创建多个图表。

## <a name="apply-filters-to-charts"></a>向图表应用筛选器

可以将筛选器应用到显示多维指标的图表。 例如，如果指标“事务计数”具有指示事务的响应成功与否的维度“响应类型”，则在此维度上进行筛选将只绘制成功（或失败）事务的折线图。 

### <a name="to-add-a-filter"></a>添加筛选器

1. 选择图表上方的“添加筛选器”

2. 选择想要筛选的维度（属性）

   ![图表上的](./media/metrics-charts/00006.png)

3. 选择想要在绘制图表时包含的维度值（此示例将显示筛选出了成功的存储事务）：

   ![指标图](./media/metrics-charts/00007.png)

4. 选择筛选值后，在“筛选选择器”之外单击将其关闭。 现在图表将显示失败的存储事务数：

   ![图表上的](./media/metrics-charts/00008.png)

5. 可以重复步骤 1-4 将多个筛选器应用到同一个图表。

## <a name="apply-splitting-to-a-chart"></a>将应用到图表拆分

可以按维度拆分指标，以直观地显示指标不同部分之间的差异，并标识出某个维度的边远部分。 

### <a name="to-apply-splitting"></a>若要将应用拆分

1. 单击图表上方的“应用拆分”。
 
   > [!NOTE]
   > 拆分不能用于具有多个指标的图表。 此外，可以有多个筛选器，但只有一个拆分维度应用于任何单个图表。

2. 选择想要用于分割图表的维度：

   ![图表上的](./media/metrics-charts/00010.png)

   现在图表将显示多个折线图，每个维度部分均有一个：

   ![图表上的](./media/metrics-charts/00012.png)

3. 在“分组选择器”之外单击以将其关闭。

   > [!NOTE]
   > 在同一个维度上同时使用筛选和拆分，可以隐藏与你的方案无关的部分，使图表更易读取。

## <a name="lock-boundaries-of-chart-y-axis"></a>锁定图表 y 轴的边界

当图表显示较大值的较小波动时，锁定 y 轴的范围变得很重要。 

例如，如果成功请求的数量从 99.99% 下降到 99.5%，这可能表示服务质量显著降低。 不过，使用默认的图表设置时，观察小的数值波动很困难，甚至不可能实现。 在这种情况下，你可以将图表的最低边界锁定到 99%，这将使此较小的降低更加明显。 

另一个示例是可用内存的波动，其中的值在技术上永远不会达到 0。 将范围固定到一个较高的值可以使可用内存的降低更容易被发现。 

若要控制 y 轴范围，请使用 “…” 图表菜单，并选择“编辑图表”以访问高级图表设置。 修改“Y 轴范围”部分中的值，或者使用“自动”按钮恢复为默认值。

![图表上的](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> 如果图表用于跟踪一段时间内的各种计数或合计（并因此使用计数、求和、最小值或最大值聚合），要锁定这类图表的 y 轴边界，通常需要指定一个固定的时间粒度，而不是依赖于自动默认值。 这是必要的，因为当用户通过调整浏览器窗口大小或者通过更改屏幕分辨率来自动修改时间粒度时，图表上的值也会发生更改。 时间粒度发生的更改会影响图表的外观，导致当前选择的 y 轴范围失效。

## <a name="pin-charts-to-dashboards"></a>将图表固定到仪表板

配置图表后，可能需要将其添加到仪表板，以便可以再次查看它（可能是在其他监视遥测的上下文中）或与团队共享。

将配置的图表固定到仪表板：

配置图表后，单击图表右上角的“图表操作”菜单，然后单击“固定到仪表板”。

![图表上的](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>创建警报规则

可以使用设置的条件将指标可视化为基于指标的警报规则的基础。 新的警报规则将包括图表的目标资源、指标、拆分和筛选器维度。 稍后将能够在警报规则创建窗格上修改这些设置。

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>单击“新建警报规则”，创建新的警报规则

![以红色突出显示的“新建警报规则”按钮](./media/metrics-charts/015.png)

则会转到警报规则创建窗格，其中预先填充了来自你的图表的底层指标维度，以便更轻松地生成自定义警报规则。

![创建警报规则](./media/metrics-charts/016.png)

若要详细了解如何设置指标警报，请查看此[文章](alerts-metric.md)。

## <a name="troubleshooting"></a>故障排除

*图表中未显示任何数据。*

* 筛选器将应用到窗格中的所有图表。 将焦点放在某个图表上时，请确保未在其他图表上设置会排除所有数据的筛选器。

* 如果想要在不同的图表上设置不同的筛选器，请在不同的边栏选项卡中创建图表，将它们保存为独立的收藏项。 如果需要，可将这些图表固定到仪表板，以便并排查看这些图表。

* 如果根据指标上未定义的属性对图表进行分段，则图表中不会显示任何数据。 请尝试清除分段（拆分），或选择其他属性。

## <a name="next-steps"></a>后续步骤

  请参阅[创建自定义 KPI 仪表板](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards)，了解使用指标创建可操作仪表板的最佳实践。

