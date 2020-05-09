---
title: Azure 指标资源管理器的高级功能
description: 了解 Azure Monitor 指标资源管理器的高级功能
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e6ff33b6a23cb85649a8811a8bef27ab455ab9e6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980892"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Azure 指标资源管理器的高级功能

> [!NOTE]
> 本文假定使用者熟悉指标资源管理器的基本功能。 如果你是新用户，希望了解如何创建第一个指标图表，请参阅 [Azure 指标资源管理器入门](metrics-getting-started.md)。

## <a name="metrics-in-azure"></a>Azure 中的指标

[Azure Monitor 中的指标](data-platform-metrics.md)是随着时间的推移收集和存储的一系列测量值和计数。 有标准（或“平台”）指标和自定义指标。 标准指标由 Azure 平台本身提供。 标准指标反映 Azure 资源的运行状况和使用情况统计信息。 而自定义指标是由应用程序通过[用于自定义事件和指标的 Application Insights API](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics)、[Windows Azure 诊断 (WAD) 扩展](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)或 [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api) 发送给 Azure 的。

## <a name="create-views-with-multiple-metrics-and-charts"></a>使用多个指标和图表创建视图

可以创建一次绘制多个指标行或显示多个指标图表的图表。 此功能用于：

- 将同一图形中的相关指标关联到一起，看一个值如何与另一个值相关联。
- 显示很临近但具有不同度量单位的指标
- 以视觉方式聚合和比较来自多个源的指标

例如，你有 5 个存储帐户，想要知道在其中消耗了多少总空间，则可创建一个（堆积的）面积图表，以便显示特定时间点的各个值以及所有值的总计。

### <a name="multiple-metrics-on-the-same-chart"></a>同一图表上的多个指标

首先，[创建新图表](metrics-getting-started.md#create-your-first-metric-chart)。 单击“添加指标”，然后通过同样的步骤在同一图表上添加另一指标。 

   > [!NOTE]
   > 通常情况下，你不会想要在一个图表上拥有度量单位不同（即“毫秒”和“千字节”）或刻度差异显著的多个指标。 此时，可考虑使用多个图表。 单击“添加图表”按钮，即可在指标资源管理器中创建多个图表。

### <a name="multiple-charts"></a>多个图表

单击“添加图表”，使用另一指标创建另一图表。 

### <a name="order-or-delete-multiple-charts"></a>将多个图表排序或将其删除

若要将多个图表排序或将其删除，请单击省略号 ( **...** )，以便打开图表菜单并选择适当的菜单项：**向上移动**、**向下移动**或**删除**。

## <a name="changing-aggregation"></a>更改聚合

向图表添加度量值时，"指标资源管理器" 会自动预选择其默认聚合。 默认情况下，这在基本方案中是有意义的，但你可以使用不同的聚合获取有关指标的更多见解。 若要查看图表中的不同聚合，需要了解指标资源管理器处理它们的方式。 

指标是在一段时间内捕获的一系列度量值（或 "度量值"）。 绘制图表时，所选度量值的值在*时间粒度*上单独聚合。 [使用指标资源管理器时间选择器面板](metrics-getting-started.md#select-a-time-range)选择时间粒度的大小。 如果没有明确地选择时间粒度，则会根据当前所选时间范围自动选择时间粒度。 确定时间粒度后，在每个时间间隔内捕获的指标值将聚合并放置在图表上，每次粒度一项。

例如，假设图表显示了使用**最近24小时**内的**平均**聚合的**服务器响应时间**度量值：

- 如果将时间粒度设置为30分钟，则将从48聚合数据点（例如折线图连接图表绘图区中的48点）绘制图表。 也就是说，每小时24小时 x 2 数据点。 每个点都表示在每个相关的30分钟内发生的服务器请求的所有已捕获响应时间的*平均值*。
- 如果将时间粒度切换到15分钟，则会获得96聚合数据点。  也就是说，每小时24小时 x 4 数据点。

指标资源管理器中提供了五种基本统计数据聚合类型： **Sum**、 **Count**、 **Min**、 **Max**和**Average**。 **Sum**聚合有时称为**总计**聚合。 对于许多指标，指标资源管理器将隐藏完全无关且不能使用的聚合。

- **Sum** –聚合间隔内捕获的所有值的总和
- **Count** –通过聚合间隔捕获的度量值的数量。 请注意，在始终用值1捕获度量值的情况下，**计数**将等于**Sum** 。 当指标跟踪不同事件的计数，并且每个度量值都表示一个事件时（即，每次发出新请求时，代码都会触发指标记录），这种情况很常见。
- **Average** –通过聚合间隔捕获的指标值的平均值
- **Min** –聚合间隔内捕获的最小值
- **Max** –聚合间隔内捕获的最大值

## <a name="apply-filters-to-charts"></a>向图表应用筛选器

可以将筛选器应用到显示多维指标的图表。 例如，如果指标“事务计数”具有指示事务的响应成功与否的维度“响应类型”，则在此维度上进行筛选将只绘制成功（或失败）事务的折线图。 

### <a name="to-add-a-filter"></a>添加筛选器

1. 选择图表上方的“添加筛选器”****

2. 选择想要筛选的维度（属性）

   ![图表上的](./media/metrics-charts/00006.png)

3. 选择想要在绘制图表时包含的维度值（此示例将显示筛选出了成功的存储事务）：

   ![图表上的](./media/metrics-charts/00007.png)

4. 选择筛选值后，在“筛选选择器”之外单击将其关闭。 现在图表将显示失败的存储事务数：

   ![图表上的](./media/metrics-charts/00008.png)

5. 可以重复步骤 1-4 将多个筛选器应用到同一个图表。



## <a name="apply-splitting-to-a-chart"></a>对图表应用拆分

可以按维度拆分指标，以直观地显示指标不同部分之间的差异，并标识出某个维度的边远部分。

### <a name="apply-splitting"></a>应用拆分

1. 单击图表上方的“应用拆分”****。
 
   > [!NOTE]
   > 不能对包含多个指标的图表使用拆分。 另外，你可以有多个筛选器，但只能对任何单个图表应用一个拆分维度。

2. 选择想要用于分割图表的维度：

   ![图表上的](./media/metrics-charts/00010.png)

   现在图表将显示多个折线图，每个维度部分均有一个：

   ![图表上的](./media/metrics-charts/00012.png)

3. 在“分组选择器”**** 之外单击以将其关闭。

   > [!NOTE]
   > 在同一个维度上同时使用筛选和拆分，可以隐藏与你的方案无关的部分，使图表更易读取。

## <a name="lock-boundaries-of-chart-y-axis"></a>锁定图表 y 轴的边界

当图表显示较大值的较小波动时，锁定 y 轴的范围变得很重要。 

例如，如果成功请求的数量从 99.99% 下降到 99.5%，这可能表示服务质量显著降低。 不过，使用默认的图表设置时，观察小的数值波动很困难，甚至不可能实现。 在这种情况下，你可以将图表的最低边界锁定到 99%，这将使此较小的降低更加明显。 

另一个示例是可用内存的波动，其中的值在技术上永远不会达到 0。 将范围固定到一个较高的值可以使可用内存的降低更容易被发现。 

若要控制 y 轴范围，请使用 “…” 图表菜单，并选择“编辑图表”**** 以访问高级图表设置。 修改“Y 轴范围”部分中的值，或者使用“自动”**** 按钮恢复为默认值。

![图表上的](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> 如果图表用于跟踪一段时间内的各种计数或合计（并因此使用计数、求和、最小值或最大值聚合），要锁定这类图表的 y 轴边界，通常需要指定一个固定的时间粒度，而不是依赖于自动默认值。 这是必要的，因为当用户通过调整浏览器窗口大小或者通过更改屏幕分辨率来自动修改时间粒度时，图表上的值也会发生更改。 时间粒度发生的更改会影响图表的外观，导致当前选择的 y 轴范围失效。

## <a name="change-colors-of-chart-lines"></a>更改图表线条的颜色

配置图表后，会自动从默认调色板中为图表线条分配颜色。 您可以更改这些颜色。

若要更改图表线条的颜色，请在与图表相对应的图例中单击彩色条。 "颜色选取器" 对话框将打开。 使用颜色选取器配置线条的颜色。

图表颜色配置完成后，当您将图表固定到仪表板时，它们将保持为这种方式。 以下部分说明如何固定图表。

![图表上的](./media/metrics-charts/018.png)

## <a name="pin-charts-to-dashboards"></a>将图表固定到仪表板

配置图表后，可能需要将其添加到仪表板，以便可以再次查看它（可能是在其他监视遥测的上下文中）或与团队共享。

将配置的图表固定到仪表板：

配置图表后，单击图表右上角的“图表操作”**** 菜单，然后单击“固定到仪表板”****。

![图表上的](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>创建警报规则

可以使用设置的条件将指标可视化为基于指标的警报规则的基础。 新的警报规则将包括图表的目标资源、指标、拆分和筛选器维度。 稍后将能够在警报规则创建窗格上修改这些设置。

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>单击“新建警报规则”，创建新的警报规则****

![以红色突出显示的“新建警报规则”按钮](./media/metrics-charts/015.png)

则会转到警报规则创建窗格，其中预先填充了来自你的图表的底层指标维度，以便更轻松地生成自定义警报规则。

![创建警报规则](./media/metrics-charts/016.png)

若要详细了解如何设置指标警报，请查看此[文章](alerts-metric.md)。

## <a name="troubleshooting"></a>疑难解答

*图表中未显示任何数据。*

* 筛选器将应用到窗格中的所有图表。 将焦点放在某个图表上时，请确保未在其他图表上设置会排除所有数据的筛选器。

* 如果想要在不同的图表上设置不同的筛选器，请在不同的边栏选项卡中创建图表，将它们保存为独立的收藏项。 如果需要，可将这些图表固定到仪表板，以便并排查看这些图表。

* 如果根据指标上未定义的属性对图表进行分段，则图表中不会显示任何数据。 请尝试清除分段（拆分），或选择其他属性。

## <a name="next-steps"></a>后续步骤

  请参阅[创建自定义 KPI 仪表板](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards)，了解使用指标创建可操作仪表板的最佳实践。

