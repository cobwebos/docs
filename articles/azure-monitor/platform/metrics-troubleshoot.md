---
title: Azure Monitor 指标图表的疑难解答
description: 排查创建、自定义或解释指标图表时遇到的问题
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e1ad4e53596b8228bdef5beb18aa250a9512c49f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659656"
---
# <a name="troubleshooting-metrics-charts"></a>指标图表疑难解答

如果在 Azure 指标资源管理器中创建、自定义或解释图表时遇到问题，请使用本文。 如果你不熟悉指标，请了解指标[资源管理](metrics-getting-started.md)器的入门和[指标资源管理器的高级功能](metrics-charts.md)。 您还可以查看配置的指标图表的[示例](metric-chart-samples.md)。

## <a name="cant-find-your-resource-to-select-it"></a>找不到要选择的资源

你单击了“选择资源”按钮，但资源选择器对话框中未显示你的资源。

**解决方案：** 指标资源管理器要求在列出可用资源之前选择订阅和资源组。 如果未显示你的资源：

1. 确保已在“订阅”下拉列表中选择了正确的订阅。 如果未列出你的订阅，请单击“目录 + 订阅设置”，然后为你的资源添加订阅。

1. 确保已选择正确的资源组。
    > [!WARNING]
    > 为达到最佳性能，首次打开指标资源管理器时，“资源组”下拉列表中没有预先选择的资源组。 必须先选择至少一个组才会显示资源。

## <a name="chart-shows-no-data"></a>图表未显示数据

有时，在选择正确的资源和指标之后，图表可能不显示任何数据。 此行为可能由以下几个原因导致：

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>未向你的订阅注册 Microsoft Insights 资源提供程序

浏览指标需要已在订阅中注册 Microsoft.Insights 资源提供程序。 多数情况下，它是自动注册的（即，在配置预警规则、为任何资源自定义诊断设置或配置自动缩放规则之后）。 如果未注册 Microsoft Insights 资源提供程序，则必须按照[Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)中所述的步骤手动注册该提供程序。

**解决方案：** 打开 "**订阅**"、"**资源提供程序**" 选项卡，并验证是否已为你的订阅注册了*Microsoft Insights* 。

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>你的资源没有足够的访问权限

在 Azure 中，[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 控制对指标的访问。 你必须是[监视读者](../../role-based-access-control/built-in-roles.md#monitoring-reader)、[监视参与者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)或[参与者](../../role-based-access-control/built-in-roles.md#contributor)成员，才能浏览任何资源的指标。

**解决方案：** 确保你对从中浏览指标的资源具有足够的权限。

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>你的资源在所选时间范围内未发出指标

一些资源不会持续发出其指标。 例如，Azure 不会收集已停用虚拟机的指标。 其他资源可能仅在某些条件发生时才发出其指标。 例如，显示事务处理时间的指标需要至少一个事务。 如果在选定时间范围内没有事务，则图表自然为空。 此外，尽管 Azure 中的大多数指标是每分钟收集一次，但也有一些指标收集频率更低。 请参阅指标文档，详细了解要浏览的指标。

**解决方案：** 将图表的时间更改为更大的范围。 你可以使用更大的时间粒度（或依赖于 "自动时间粒度" 选项）从 "过去30天" 开始。

### <a name="you-picked-a-time-range-greater-than-30-days"></a>选取的时间范围大于30天

[Azure 中的大多数指标可存储 93 天](data-platform-metrics.md#retention-of-metrics)。 但你在任何单个图表上仅可查询不超过 30 天的数据。 此限制不适用于[基于日志的指标](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)。

**解决方案：** 如果看到空白图表或图表只显示部分指标数据，请验证时间选取器中的开始日期和结束日期之间的差异是否不超过30天时间间隔。

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>所有指标值都超出锁定的 y 轴范围

[锁定图表 y 轴的边界](metrics-charts.md#lock-boundaries-of-chart-y-axis)可能会无意中使图表显示区域不显示图表线。 例如，如果将 y 轴锁定在 0% 到 50% 的范围内，且指标值为常数值 100%，则线将始终呈现在可见区域之外，导致图表显示为空白。

**解决方案：** 验证图表的 y 轴边界未锁定在指标值的范围之外。 如果 y 轴边界已锁定，可能需要将其临时重置，以确保指标值不超出图表范围。 对于带有 sum、min 和 max 聚合的图表，不建议使用自动粒度锁定 y 轴范围，因为调整浏览器窗口大小或从一个屏幕分辨率切换到另一个屏幕分辨率时图表的值将随着粒度的变化而变化。 切换粒度可能导致图表的显示区域为空。

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>正在查看来宾 OS 指标，但未启用 Azure 诊断扩展

收集来宾 OS 指标需要配置 Azure 诊断扩展，或使用资源的“诊断设置”面板启用 Azure 诊断扩展。

**解决方案：** 如果已启用 Azure 诊断扩展，但仍无法查看指标，请按照[Azure 诊断扩展故障排除指南](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)中所述的步骤进行操作。 另请参阅[无法选取来宾操作系统命名空间和指标](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)的故障排除步骤

## <a name="error-retrieving-data-message-on-dashboard"></a>仪表板上的 "检索数据时出错" 消息

如果使用了之后弃用并从 Azure 中删除的指标创建仪表板，可能会导致此问题发生。 要验证是否是这种情况，请打开资源的“指标”选项卡，然后在指标选择器中勾选可用指标。 如果未显示某指标，则该指标已从 Azure 中删除。 通常，如果某指标被弃用，会有更好的新指标提供对资源运行状况的类似见解。

**解决方案：** 通过在仪表板上为图表选取替代指标来更新失败磁贴。 可[查看 Azure 服务的可用指标列表](metrics-supported.md)。

## <a name="chart-shows-dashed-line"></a>图表显示虚线

Azure 指标图表使用虚线样式来指示两个已知的时间粒度数据点之间是否缺少值（也称为 "null 值"）。 例如，如果在时间选择器中选择了 "1 分钟" 时间粒度，但指标报告时间为07:26、07:27、07:29 和07:30 （请注意，第二个和第三个数据点之间的分钟间隔），则虚线将连接07:27 和07:29，实线将连接所有其他数据点。 当度量值使用**count**和**sum**聚合时，虚线将向下降到零。 对于**avg**、 **min**或**max**聚合，虚线连接两个最近已知的数据点。 此外，当图表最右边或最左边的数据缺失时，虚线将扩展到缺失数据点的方向。
  ![指标图像](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**解决方案：** 此行为是设计的。 这对于确定缺失数据点很有用。 折线图是直观的高密度度量值的理想选择，但可能难以使用稀疏值来解释指标，尤其是在 corelating 具有时间粒度的值非常重要时。 借助虚线，可以更轻松地查看这些图表，但如果图表仍然不清楚，请考虑使用其他图表类型查看指标。 例如，对于同一指标，具有相同指标的分散图清楚地显示每个时间粒度，只是在存在值时可视化点并在缺少值时完全跳过数据点： ![指标图像](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > 如果你仍然喜欢为指标使用折线图，将鼠标移到图表上可能有助于通过突出显示鼠标指针位置处的数据点来评估时间粒度。

## <a name="chart-shows-unexpected-drop-in-values"></a>图表显示意外的下拉值

在许多情况下，感知到的指标值减小是对图表上显示的数据的误解。 当图表显示最近几分钟的值时，你可能会被总和或计数的减小所误导，因为 Azure 尚未收到或处理最后的指标数据点。 处理指标的延迟可能在几分钟的时间范围内，具体取决于服务。 对于显示最新时间范围为1或5分钟的图表，最后几分钟内的值的放置将变得更为明显： ![指标图像](./media/metrics-troubleshoot/drop-in-values.png)

**解决方案：** 此行为是设计的。 我们认为即使数据是部分或不完整的，在我们收到数据后立即显示数据也是有益的。 这样做可以让你更快地得到重要结论，并立即开始调查。 例如，对于显示故障次数的指标，显示部分值 X 表示在给定的分钟内至少发生了 X 次故障。 你可以立即开始调查问题，而不是等待查看此分钟发生的确切故障次数，确切故障次数可能并不重要。 一旦我们收到整套数据，图表就会更新，但此时它也可能会显示最近几分钟内新的不完整数据点。

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>无法选取来宾操作系统命名空间和指标

虚拟机和虚拟机规模集具有两类指标： Azure 宿主环境收集的**虚拟机主机**度量值，以及虚拟机上运行的[监视代理](agents-overview.md)收集的**来宾 OS （经典）** 指标。 通过启用 [Azure 诊断扩展](diagnostics-extension-overview.md)安装监视代理。

来宾 OS 指标默认存储在 Azure 存储帐户中，可从资源的“诊断设置”选项卡中进行选择。 如果未收集来宾 OS 指标或指标资源管理器无法访问来宾 OS 指标，将仅显示虚拟机主机指标命名空间：

![图表上的](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**解决方案：** 如果在指标资源管理器中看不到**来宾 OS （经典）** 命名空间和度量值：

1. 确认 [Azure 诊断扩展](diagnostics-extension-overview.md)已启用且配置为收集指标。
    > [!WARNING]
    > 无法使用 [Log Analytics 代理](agents-overview.md#log-analytics-agent)（也称为 Microsoft Monitoring agent 或“MMA”）将来宾 OS 发送到存储帐户。

1. 确保为[你的订阅注册](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription)了**Microsoft Insights**资源提供程序。

1. 验证存储帐户是否不受防火墙保护。 Azure 门户需要对存储帐户的访问权限才能检索指标数据和绘制图表。

1. 使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)验证指标是否流入存储帐户。 如果未收集指标，请遵循 [Azure 诊断扩展故障排除指南](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)进行操作。

## <a name="next-steps"></a>后续步骤

* [了解指标资源管理器入门](metrics-getting-started.md)
* [了解指标资源管理器的高级功能](metrics-charts.md)
* [查看 Azure 服务的可用指标列表](metrics-supported.md)
* [查看已配置图表的示例](metric-chart-samples.md)
