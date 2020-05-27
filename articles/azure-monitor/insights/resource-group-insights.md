---
title: Azure Monitor 资源组见解 |Microsoft Docs
description: 通过 Azure Monitor 了解资源组级别的分布式应用程序和服务的运行状况和性能
ms.subservice: ''
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 06bdedf1dac92a2010718ffd3eb5c6e43bb51e6c
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798120"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>使用 Azure Monitor（预览版）监视资源组

现代应用程序通常很复杂且分布广泛，许多独立部件协同工作来提供服务。 Azure Monitor 考虑到这种复杂性，为资源组提供监视见解。 因此，可以轻松分类和诊断各资源出现的任何问题，同时提供有关资源组和应用程序的运行状况和性能的整体上下文&mdash;&mdash;。

## <a name="access-insights-for-resource-groups"></a>资源组的访问见解

1. 在左侧的导航栏中选择“资源组”。
2. 选择其中一个要浏览的资源组。 （如果拥有大量资源组，则按订阅筛选有时会很有用。）
3. 若要访问资源组的见解，单击任意资源组的左侧菜单中的“见解”。

![资源组见解概述页的屏幕截图](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>具有活动警报和运行状况问题的资源

概述页显示已触发且仍处于活动状态的警报数以及每个资源的当前 Azure 资源运行状况。 这些信息有助于快速发现出现问题的任何资源。 警报可帮助检测代码中的问题以及配置基础结构的方式。 Azure 资源运行状况显示 Azure 平台本身的问题，这些问题并非特定于单个应用程序。

![Azure 资源运行状况窗格的屏幕截图](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure 资源运行状况

若要显示 Azure 资源运行状况，选中表格上方的“显示 Azure 资源运行状况”。 默认情况下隐藏此列，以快速加载页面。

![添加了资源运行状况图的屏幕截图](./media/resource-group-insights/0003-overview.png)

默认情况下，按照应用层和资源类型对资源进行分组。 应用层是资源类型的简单分类，仅存在于资源组见解概述页的上下文中。 存在与应用程序代码、计算基础结构、网络、存储 + 数据库相关的资源类型。 管理工具具有自己的应用层，每个其他资源都归类为属于“其他”应用层。 此分组可以帮助快速查看应用程序的哪些子系统运行正常，哪些子系统运行不正常。

## <a name="diagnose-issues-in-your-resource-group"></a>诊断资源组中的问题

资源组见解页提供一些可帮助诊断问题的其他工具

   |         |          |
   | ---------------- |:-----|
   | [**警报**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  查看、创建和管理警报。 |
   | [**指标**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | 可视化并浏览基于指标的数据。    |
   | [**活动日志**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Azure 中已发生的订阅级别事件。  |
   | [**应用程序映射**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | 浏览分布式应用程序的拓扑，以识别性能瓶颈或故障作用点。 |

## <a name="failures-and-performance"></a>故障和性能

如果发现应用程序运行缓慢或用户报告了错误，该怎么办？ 搜索所有资源以隔离问题是非常耗时的。

“性能”和“故障”选项卡通过结合使用许多常见资源类型的性能和故障诊断视图，简化了此过程 。

大多数资源类型将打开 Azure Monitor 工作簿模板库。 可以自定义、保存、与团队共享创建的每个工作簿，并在将来重新用于诊断类似问题。

### <a name="investigate-failures"></a>调查故障

若要测试“故障”选项卡，选择左侧菜单中“调查”下的“故障” 。

选择完成后，左侧菜单栏会发生更改，提供新选项。

![故障概述窗格的屏幕截图](./media/resource-group-insights/00004-failures.png)

选择应用服务时，会看到 Azure Monitor 工作簿模板库。

![应用程序工作簿库的屏幕截图](./media/resource-group-insights/0005-failure-insights-workbook.png)

选择故障见解的模板会打开工作簿。

![故障报告的屏幕截图](./media/resource-group-insights/0006-failure-visual.png)

可以选择任意行。 然后所选内容显示在图形的详细信息视图中。

![故障详细信息的屏幕截图](./media/resource-group-insights/0007-failure-details.png)

工作簿将创建自定义报告和可视化效果的困难工作抽象化为易于使用的格式。 虽然一些用户可能只想调整预生成的参数，但工作簿是完全可自定义的。

若要了解此工作簿是如何在内部运作的，选择顶栏的“编辑”。

![其他编辑选项的屏幕截图](./media/resource-group-insights/0008-failure-edit.png)

许多“编辑”框将显示在工作簿的各种元素周围。 选择操作表下方的“编辑”框。

![编辑框的屏幕截图](./media/resource-group-insights/0009-failure-edit-graph.png)

这显示了生成表的可视化效果的底层日志查询。

 ![日志查询窗口的屏幕截图](./media/resource-group-insights/0010-failure-edit-query.png)

可以直接修改查询。 还可以将其作为参考，并在设计自己的自定义参数化工作簿时从中借用。

### <a name="investigate-performance"></a>调查性能

性能提供其自己的工作簿库。 对于应用服务，预生成的应用程序性能工作簿提供以下视图：

 ![性能视图的屏幕截图](./media/resource-group-insights/0011-performance.png)

在这种情况下，如果选择编辑，将看到这组可视化效果由 Azure Monitor Metrics 提供支持。

 ![Azure Metrics 的性能视图的屏幕截图](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>疑难解答

### <a name="enabling-access-to-alerts"></a>启用对警报的访问

若要在适用于资源组的 Azure Monitor 中查看警报，需要由此订阅中具有“所有者”或“参与者”角色的某人为订阅中的任何资源组打开适用于资源组的 Azure Monitor。 这会使得具有读取访问权限的任何人都能够在适用于资源组的 Azure Monitor 中查看针对该订阅中所有资源组的警报。 如果你拥有“所有者”或“参与者”角色，请在几分钟后刷新此页面。

适用于资源组的 Azure Monitor 依赖于 Azure Monitor 警报管理系统来检索警报状态。 默认情况下没有为每个资源组和订阅配置“警报管理”，它只能由具有“所有者”或“参与者”角色的某人来启用。 可以通过以下任一方式启用它：
* 为订阅中的任何资源组打开适用于资源组的 Azure Monitor。
* 或者，转到订阅，单击“资源提供程序”，然后单击“注册 Alerts.Management”。

## <a name="next-steps"></a>后续步骤

- [Azure Monitor 工作簿](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)
- [Azure 资源运行状况](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Azure Monitor 警报](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
