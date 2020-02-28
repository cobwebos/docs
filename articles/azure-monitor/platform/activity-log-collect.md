---
title: 在 Log Analytics 工作区中收集和分析 Azure 活动日志 |Microsoft Docs
description: 收集 Azure 活动日志 Azure Monitor 日志，并使用监视解决方案跨所有 Azure 订阅分析和搜索 Azure 活动日志。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 044f974d83eba098820639e67412110329d5ad7d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668972"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>收集和分析 Azure Monitor 中 Log Analytics 工作区中的 Azure 活动日志

> [!WARNING]
> 你现在可以使用类似于收集资源日志的方式的诊断设置，将活动日志收集到 Log Analytics 工作区中。 请参阅[在 Azure Monitor 中的 Log Analytics 工作区中收集和分析 Azure 活动日志](diagnostic-settings-legacy.md)。

[Azure 活动日志](platform-logs-overview.md)提供对 Azure 订阅中发生的订阅级别事件的见解。 本文介绍如何将活动日志收集到 Log Analytics 工作区中，以及如何使用 Activity Log Analytics[监视解决方案](../insights/solutions.md)，该解决方案提供用于分析此数据的日志查询和视图。 

将活动日志连接到 Log Analytics 工作区具有以下优势：

- 将多个 Azure 订阅中的活动日志合并到一个位置以进行分析。
- 将活动日志条目存储超过90天。
- 将活动日志数据与 Azure Monitor 收集的其他监视数据相关联。
- 使用[日志查询](../log-query/log-query-overview.md)执行复杂的分析，并深入了解活动日志条目。

## <a name="connect-to-log-analytics-workspace"></a>连接到 Log Analytics 工作区
单个工作区可连接到同一 Azure 租户中多个订阅的活动日志。 对于跨多个租户的集合，请参阅[跨不同 Azure Active Directory 租户中的订阅将 Azure 活动日志收集到 Log Analytics 工作区中](activity-log-collect-tenants.md)。

> [!IMPORTANT]
> 如果没有为你的订阅注册 Microsoft.operationalinsights 和为 microsoft.operationsmanagement 资源提供程序，则可能会收到包含以下过程的错误。 若要注册这些提供程序，请参阅[Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)。

使用以下过程将活动日志连接到 Log Analytics 工作区：

1. 从 Azure 门户中的 " **Log Analytics 工作区**" 菜单中，选择工作区以收集活动日志。
1. 在工作区菜单的 "**工作区数据源**" 部分中，选择 " **Azure 活动日志**"。
1. 单击要连接的订阅。

    ![工作区](media/activity-log-export/workspaces.png)

1. 单击 "**连接**" 将订阅中的活动日志连接到所选工作区。 如果订阅已连接到其他工作区，请单击 "首先**断开**连接" 断开连接。

    ![连接工作区](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>在 Log Analytics 工作区中分析
将活动日志连接到 Log Analytics 工作区时，会将条目写入工作区中名为**AzureActivity**的表，您可以使用[日志查询](../log-query/log-query-overview.md)进行检索。 此表的结构因[日志条目的类别](activity-log-view.md#categories-in-the-activity-log)而异。 有关每个类别的说明，请参阅[Azure 活动日志事件架构](activity-log-schema.md)。

## <a name="activity-logs-analytics-monitoring-solution"></a>活动日志分析监视解决方案
Azure Log Analytics 监视解决方案包含多个日志查询和视图，用于分析 Log Analytics 工作区中的活动日志记录。

### <a name="install-the-solution"></a>安装解决方案
使用[安装监视解决方案](../insights/solutions.md#install-a-monitoring-solution)中的过程来安装**Activity Log Analytics**解决方案。 不需要其他配置。

### <a name="use-the-solution"></a>使用解决方案
可以从 Azure 门户中的 "**监视**" 菜单访问监视解决方案。 在 "**见解**" 部分中选择 "**更多**"，打开包含 "解决方案" 磁贴的 "**概述**" 页。 " **Azure 活动日志**" 磁贴显示工作区中**AzureActivity**记录数的计数。

![Azure 活动日志磁贴](media/collect-activity-logs/azure-activity-logs-tile.png)


单击 " **Azure 活动日志**" 磁贴，打开 " **azure 活动日志**" 视图。 此视图包括下表中的可视化部件。 每个部分都列出了最多10个与该部件在指定时间范围内的条件匹配的项。 您可以通过单击部分底部的 "**查看全部**" 来运行返回所有匹配记录的日志查询。

![Azure 活动日志仪表板](media/collect-activity-logs/activity-log-dash.png)

| 可视化部件 | 说明 |
| --- | --- |
| Azure 活动日志条目 | 显示所选日期范围的排名靠前的 Azure 活动日志条目的条形图，并显示前10个活动调用方的列表。 单击该条形图可针对 `AzureActivity` 运行日志搜索。 单击 "调用方" 项可运行日志搜索，返回该项目的所有活动日志条目。 |
| 按状态分类的活动日志 | 显示所选日期范围的 Azure 活动日志状态的圆环图以及前十种状态记录的列表。 单击该图表以运行 `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`的日志查询。 单击某一状态项可运行日志搜索，返回该状态记录的所有活动日志条目。 |
| 按资源分类的活动日志 | 显示具有活动日志的总资源数，并列出每个资源的记录计数前十个资源。 单击全部区域可针对 `AzureActivity | summarize AggregatedValue = count() by Resource` 运行日志搜索，这会显示解决方案可以使用的所有 Azure 资源。 单击资源可运行返回该资源所有活动记录的日志查询。 |
| 按资源提供程序分类的活动日志 | 显示生成活动日志的资源提供程序的总数，并列出前10个。 单击 "总计" 区域可运行 `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`的日志查询，该查询将显示所有 Azure 资源提供程序。 单击资源提供程序以运行返回提供程序的所有活动记录的日志查询。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[活动日志](platform-logs-overview.md)。
- 详细了解[Azure Monitor 数据平台](data-platform.md)。
- 使用[日志查询](../log-query/log-query-overview.md)查看活动日志中的详细信息。
