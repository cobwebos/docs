---
title: Azure Monitor 指标图表故障排除
description: 创建、 自定义，或解释指标图表的问题进行故障排除
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 73ef5cc00b5154dbdbc92911d17740c7d13038ec
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341982"
---
# <a name="troubleshooting-metrics-charts"></a>度量值图表故障排除

如果遇到与创建、 自定义，或解释 Azure 指标资源管理器中的图表的问题，请参考本文。 如果您不熟悉指标，了解如何[指标资源管理器入门](metrics-getting-started.md)并[指标资源管理器的高级功能](metrics-charts.md)。 你还可以看到[示例](metric-chart-samples.md)的已配置的指标图表。

## <a name="cant-find-your-resource-to-select-it"></a>找不到你的资源以将其选中

在您单击过**选择一个资源**按钮，但看不到你在资源选取器对话框中的资源。

**解决方案：** 指标资源管理器要求您列出可用的资源之前，选择订阅和资源组。 如果看不到所需的资源：

1. 确保已选择正确的订阅中**订阅**下拉列表。 如果未列出你的订阅，请单击**目录 + 订阅设置**并添加具有所需的资源的订阅。

1. 确保已选择正确的资源组。
    > [!WARNING]
    > 为了获得最佳性能，当你首次打开指标资源管理器**资源组**下拉列表中具有任何预先选择的资源组。 可以看到任何资源之前，必须选择至少一个组。

## <a name="chart-shows-no-data"></a>图表显示了任何数据

有时图表可能会选择正确的资源和度量值后显示任何数据。 此行为可能导致由几个原因如下：

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>为你的订阅未注册 Microsoft.Insights 资源提供程序

浏览指标需要*Microsoft.Insights*中你的订阅注册资源提供程序。 在许多情况下，它是自动注册 （即之后配置警报规则、 自定义任何资源的诊断设置或配置自动缩放规则）。 如果未注册 Microsoft.Insights 资源提供程序，则必须手动注册它按照以下步骤中所述[Azure 资源提供程序和类型](../../azure-resource-manager/resource-manager-supported-services.md)。

**解决方案：** 打开**订阅**，**资源提供程序**选项卡，并验证*Microsoft.Insights*为订阅注册。

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>你没有访问所需的资源的足够权限

在 Azure 中，对度量值的访问控制通过[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md)。 您必须是属于[监视读取者](../../role-based-access-control/built-in-roles.md#monitoring-reader)，[监视参与者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)，或[参与者](../../role-based-access-control/built-in-roles.md#contributor)浏览任何资源的指标。

**解决方案：** 请确保有足够的权限来浏览度量值的资源。

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>所需的资源未在所选的时间范围内发出指标

某些资源不不断地发出其指标。 例如，Azure 不会收集已停止虚拟机的指标。 其他资源可能会发生某些条件时才发出其指标。 例如，显示事务的处理时间的指标需要至少一个事务。 如果没有在所选的时间范围内没有事务，图表将自然地为空。 此外，虽然大部分在 Azure 中的度量值收集每隔一分钟，但有一些不太频繁地收集的。 请参阅指标的文档以获取有关想要浏览的指标的更多详细信息。

**解决方案：** 将图表的时间更改为范围更广。 你可能会从"过去 30 天"开始使用较大的时间粒度 （或依赖于"自动时间粒度"选项）。

### <a name="you-picked-a-time-range-greater-than-30-days"></a>选取大于 30 天的时间范围

[在 Azure 中的大多数指标存储 93 天](data-platform-metrics.md#retention-of-metrics)。 但是，您可以仅查询不超过 30 天内接收所有的单个图表上的数据。 此限制不适用于[基于日志的指标](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)。

**解决方案：** 如果您将看到一个空白图表或图表仅显示指标数据的一部分，请验证之间开始的日期和结束的日期时间选取器中的差异不超过 30 天的间隔。

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>锁定 y 轴范围之外的所有指标值

通过[锁定图表 y 轴的边界](metrics-charts.md#lock-boundaries-of-chart-y-axis)，可能无意中使不会显示图表线的图表显示区域。 例如，如果在 y 轴已锁定到介于 0%和 50%，并且该度量值的常量值为 100%，使图表显示为空白的可见区域之外始终呈现在行。

**解决方案：** 验证在图表的 y 轴边界不会被锁定的指标值的范围之外。 如果 y 轴边界处于锁定状态，你可能想要暂时重置密码，确保值不超出图表区域范围的指标。 锁定 y 轴范围不建议将具有与图表自动粒度**总和**， **min**，和**最大**聚合因为它们的值将更改与通过调整浏览器窗口或从一个屏幕分辨率转到另一个的粒度。 切换粒度可能会使图表为空的显示区域。

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>您正在查看的来宾 OS 指标，但未启用 Azure 诊断扩展

集合**来宾 OS**度量值都需要配置 Azure 诊断扩展或使其使用**诊断设置**面板的所需的资源。

**解决方案：** 如果已启用 Azure 诊断扩展但仍无法看到你的度量值，请按照中所述步骤[Azure 诊断扩展故障排除指南](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)。 另请参阅故障排除步骤[不能选择来宾 OS 命名空间和指标](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>在仪表板上的"检索数据时出错"消息

使用的更高版本已弃用并从 Azure 中删除某个度量值创建你的仪表板时，可能会发生此问题。 若要验证它是这种情况，请打开**指标**资源和检查指标选取器中的可用指标的选项卡。 如果指标未显示，具有已从 Azure 中删除该度量值。 通常情况下，当某个指标已弃用，是更好地新的度量值，它提供类似的角度来看，资源运行状况。

**解决方案：** 通过选择仪表板上图表的度量指标更新故障磁贴。 你可以[查看的 Azure 服务的可用指标列表](metrics-supported.md)。

## <a name="chart-shows-dashed-line"></a>图表显示了虚线

Azure 指标图表使用虚线样式以指示存在缺失值 （也称为"null 值"） 之间两个已知的时间粒度为数据点。 例如，如果在时间选择器中选择"1 分钟"时间粒度，但该度量值报告在 07:26，07:27，07:29 和 07:30 （请注意第二个和第三个数据点的分钟差异），然后 07:27 和 07:29，虚线将连接并将连接一条实线所有其他数据点。 减少到零时指标使用虚线下降**计数**并**总和**聚合。 有关**avg**， **min**或**最大**聚合，虚线连接两个最接近的已知的数据点。 此外，当数据在图表的最右侧或最左侧一侧上缺少时，虚线将扩展到丢失的数据点的方向。
  ![图表上的](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**解决方案：** 这是设计的行为。 它可用于标识缺失的数据点。 折线图是首选用于可视化的高密度指标的趋势，但可能很难解释使用稀疏值的度量值，尤其是在 corelating 具有时间粒度值非常重要。 虚线使这些图表读取更容易，但如果您的图表仍不清楚，请考虑使用不同的图表类型查看你的度量值。 例如，在同一个指标的情况下的离散绘图图表清楚地显示由每个时间粒度仅可视化圆点，当一个值，并跳过数据点完全时缺少的值：![图表上的](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > 如果您仍想用于指标的折线图，将鼠标移动图表上可能有助于评估通过突出显示鼠标指针位置的数据点的时间粒度。

## <a name="chart-shows-unexpected-drop-in-values"></a>图表显示在值中意外的删除

在许多情况下中的指标值的感知的下拉是错误地理解图表上显示的数据。 总和下降可能产生误导或对图表时显示的最新的分钟数，因为未收到最后一个指标数据点或尚未处理由 Azure 进行计数。 具体取决于该服务处理度量值的延迟可能是几分钟时间范围内。 为图表显示使用 1 或 5 分钟粒度的最新时间范围，过去的几分钟内值的下拉会变得更明显：![图表上的](./media/metrics-troubleshoot/drop-in-values.png)

**解决方案：** 这是设计的行为。 我们认为，只要我们收到显示的数据是有益的数据时甚至*分部*或*不完整*。 这样，你可以进行更快地重要结论并开始立即调查。 例如，显示失败的次数的指标，看到的部分值 X 告诉您，没有至少 X 上给定分钟的故障。 可以开始，调查该问题，而不是等待以查看在此分钟，这可能不是那么重要上发生的失败的确切数目。 图表将更新后我们收到的整个组数据，但在该时间它可能还显示新的不完整的数据点，从较新的分钟。

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>不能选择来宾 OS 命名空间和指标

虚拟机和虚拟机规模集具有两个类别的度量值：**虚拟机主机**Azure 的托管环境中，通过收集的指标并**来宾 OS**收集的指标[监视代理](agents-overview.md)虚拟机上运行。 通过启用安装监视代理[Azure 诊断扩展](diagnostics-extension-overview.md)。

默认情况下，来宾 OS 指标存储在 Azure 存储帐户，从选取**诊断设置**的所需的资源的选项卡。 如果不收集的来宾 OS 指标或指标资源管理器无法访问它们，您将只能看到**虚拟机主机**度量命名空间：

![图表上的](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**解决方案：** 如果没有看到**来宾 OS**命名空间和指标资源管理器中的指标：

1. 确认[Azure 诊断扩展](diagnostics-extension-overview.md)已启用并配置为收集指标。
    > [!WARNING]
    > 不能使用[Log Analytics 代理](agents-overview.md#log-analytics-agent)（也称为 Microsoft Monitoring Agent 或"MMA"） 发送**来宾 OS**到存储帐户。

1. 验证存储帐户不受防火墙保护。

1. 使用[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)验证度量值流入到存储帐户。 如果不收集度量值，请按照[Azure 诊断扩展故障排除指南](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)。

## <a name="next-steps"></a>后续步骤

* [了解有关如何开始使用指标资源管理器](metrics-getting-started.md)
* [了解有关指标资源管理器的高级功能](metrics-charts.md)
* [查看 Azure 服务的可用指标列表](metrics-supported.md)
* [请参阅配置图表的示例](metric-chart-samples.md)
