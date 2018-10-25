---
title: Azure Monitor 指标资源管理器
description: 了解 Azure Monitor 指标资源管理器中的新功能
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.component: metrics
ms.openlocfilehash: f82b4dff20e2b26e62889c41b3ff3c27bc86066a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901407"
---
# <a name="azure-monitor-metrics-explorer"></a>Azure Monitor 指标资源管理器

Azure Monitor 指标资源管理器是 Microsoft Azure 门户的一个组件，通过它可以绘制图表、以直观的形式关联趋势及研究指标值的上升和下降。 指标资源管理器是研究 Azure 托管的或 Azure Monitor 服务监视的应用程序，以及基础结构存在的各种性能问题和可用性问题的基本起始点。 

## <a name="what-are-metrics-in-azure"></a>Azure 中的指标是什么？

Microsoft Azure 中的指标是随着时间的推移收集和存储的一系列测量值和计数。 有标准（或“平台”）指标和自定义指标。 标准指标由 Azure 平台本身提供。 标准指标反映 Azure 资源的运行状况和使用情况统计信息。 而自定义指标是由应用程序通过[自定义事件的 Application Insights API](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) 发送给 Azure 的。 自定义指标与其他特定于应用程序的指标存储在 Application Insights 资源中。

## <a name="how-do-i-create-a-new-chart"></a>如何创建新图表？

1. 打开 Azure 门户
2. 导航到新的“监视”选项卡，并选择“指标”。

   ![指标图像](./media/monitoring-metric-charts/0001.png)

3. “指标选择器”将自动打开。 从列表中选择一个资源，查看它关联的指标。 列表将只显示具有指标的资源。

   ![指标图像](./media/monitoring-metric-charts/0002.png)

   > [!NOTE]
   >如果有多个 Azure 订阅，指标资源管理器将拉取“门户设置 -> 按订阅筛选”列表中选定的所有订阅中的资源。 若要更改此设置，请单击屏幕顶部的“门户设置”齿轮图标，然后选择要使用的订阅。

4. 对于某些资源类型（即存储帐户和虚拟机），选择指标前必须先选择**命名空间**。 每个命名空间都带有自己的指标集，这些指标只与此命名空间有关，而与其他命名空间无关。

   例如，每个 Azure 存储空间都包含针对次级服务“Blob”、“文件”、“队列”和“表”（这些均是存储帐户的组成部分）的指标。 但是，指标“队列消息计数”必然只适用于次级服务“队列”，而不适用于任何其他存储帐户次级服务。

   ![指标图像](./media/monitoring-metric-charts/0003.png)

5. 从列表中选择一个指标。 如果知道所需指标的部分名称，可以将其键入，即可看到可用指标的筛选列表：

   ![指标图像](./media/monitoring-metric-charts/0004.png)

6. 选择指标后，将显示图表，其中包含对所选指标的默认聚合。 此时，在“指标选择器”之外单击即可将其关闭。 也可以选择将图表切换到不同的聚合。 对于一些指标，可以通过切换聚合选择想要在图表上看到的值。 例如，可以在平均值、最小值和最大值之间切换。 

7. 通过单击“添加指标”图标 ![指标图标](./media/monitoring-metric-charts/icon001.png) 并重复步骤 3-6，你可以在同一个图表上添加其他指标。

   > [!NOTE]
   > 通常情况下，你不会想要在一个图表上拥有度量单位不同（即“毫秒”和“千字节”）或刻度差异显著的多个指标。 此时，可考虑使用多个图表。 单击“添加图表”按钮，即可在指标资源管理器中创建多个图表。

## <a name="how-do-i-apply-filters-to-the-charts"></a>如何将筛选器应用到图表？

可以将筛选器应用到显示多维指标的图表。 例如，如果指标“事务计数”具有指示事务的响应成功与否的维度“响应类型”，则在此维度上进行筛选将只绘制成功（或失败）事务的折线图。 

### <a name="to-add-a-filter"></a>添加筛选器：

1. 单击“添加筛选器”图标 ![图表上的](./media/monitoring-metric-charts/icon002.png) 筛选器图标

2. 选择想要筛选的维度（属性）

   ![图表上的](./media/monitoring-metric-charts/0006.png)

3. 选择想要在绘制图表时包含的维度值（此示例将显示筛选出了成功的存储事务）：

   ![指标图](./media/monitoring-metric-charts/0007.png)

4. 选择筛选值后，在“筛选选择器”之外单击将其关闭。 现在图表将显示失败的存储事务数：

   ![图表上的](./media/monitoring-metric-charts/0008.png)

5. 可以重复步骤 1-4 将多个筛选器应用到同一个图表。

## <a name="how-do-i-segment-a-chart"></a>如何拆分图表？

可以按维度拆分指标，以直观地显示指标不同部分之间的差异，并标识出某个维度的边远部分。 

### <a name="to-segment-a-chart"></a>分割图表：

1. 单击“添加分组”图标  ![图表上的](./media/monitoring-metric-charts/icon003.png) 指标图。
 
   > [!NOTE]
   > 所有的单个图表上可以包含多个筛选器，但只能包含一个分组。

2. 选择想要用于分割图表的维度： 

   ![图表上的](./media/monitoring-metric-charts/0010.png)

   现在图表将显示多个折线图，每个维度部分均有一个：

   ![图表上的](./media/monitoring-metric-charts/0012.png)

3. 在“分组选择器”之外单击以将其关闭。

   > [!NOTE]
   > 在同一个维度上同时使用筛选器和分组，可隐藏与你的方案无关的部分，使图表更易读取。

## <a name="how-do-i-pin-charts-to-dashboards"></a>如何将图表固定到仪表板？

配置图表后，可能需要将其添加到仪表板，以便可以再次查看它（可能是在其他监视遥测的上下文中）或与团队共享。 

将配置的图表固定到仪表板：

配置图表后，单击图表右上角的“图表操作”菜单，然后单击“固定到仪表板”。

   ![图表上的](./media/monitoring-metric-charts/0013.png)

## <a name="next-steps"></a>后续步骤

  请参阅[创建自定义 KPI 仪表板](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards)，了解使用指标创建可操作仪表板的最佳实践。