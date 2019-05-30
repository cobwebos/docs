---
title: 收集和分析 Log Analytics 工作区中的 Azure 活动日志 |Microsoft Docs
description: 收集在 Azure Monitor 日志中的 Azure 活动日志，并使用监视解决方案分析和搜索所有 Azure 订阅的 Azure 活动日志。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 5839fd40a128097e400f13acbe4fb6ef90c656b7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248124"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>收集和分析 Azure Monitor 的 Log Analytics 工作区中的 Azure 活动日志
[Azure 活动日志](activity-logs-overview.md)深入了解发生在 Azure 订阅中的订阅级别事件。 本文介绍了如何将活动日志收集到 Log Analytics 工作区以及如何使用活动 Log Analytics[监视解决方案](../insights/solutions.md)，后者提供的日志查询和视图来分析此数据。 

将活动日志连接到 Log Analytics 工作区提供以下优势：

- 将合并到一个位置以进行分析的多个 Azure 订阅中的活动日志。
- 存储时间超过 90 天的活动日志的条目。
- 将活动日志数据与其他 Azure Monitor 收集的监视数据相关联。
- 使用[记录查询](../log-query/log-query-overview.md)来执行复杂的分析和深入了解活动日志条目。

## <a name="connect-to-log-analytics-workspace"></a>连接到 Log Analytics 工作区
活动日志可以连接到只有一个工作区，但单个工作区可以连接到活动日志以相同的 Azure 租户中的多个订阅。 有关跨多个租户的集合，请参阅[到跨不同的 Azure Active Directory 中的订阅的 Log Analytics 工作区中收集 Azure 活动日志租户](activity-log-collect-tenants.md)。

使用以下过程将活动日志连接到 Log Analytics 工作区：

1. 从**Log Analytics 工作区**菜单在 Azure 门户中，选择要收集活动日志的工作区。
1. 在中**工作区数据源**部分中的工作区的菜单中，选择**Azure 活动日志**。
1. 单击你想要连接的订阅。

    ![工作区](media/activity-log-export/workspaces.png)

1. 单击**Connect**连接到所选工作区的订阅中的活动日志。 如果订阅已连接到另一个工作区中，单击**断开连接**首先要断开与其的连接。

    ![连接的工作区](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>在 Log Analytics 工作区中分析
在活动日志连接到 Log Analytics 工作区时，项将写入到工作区名为的表**AzureActivity** ，可使用来检索[日志查询](../log-query/log-query-overview.md)。 此表的结构而异，具体取决于[类别的日志条目](activity-logs-overview.md#categories-in-the-activity-log)。 请参阅[Azure 活动日志事件架构](activity-log-schema.md)有关每个类别的说明。

## <a name="activity-logs-analytics-monitoring-solution"></a>活动日志分析监视解决方案
Azure Log Analytics 监视解决方案包含多个日志查询和分析 Log Analytics 工作区中的活动日志记录的视图。

### <a name="install-the-solution"></a>安装解决方案
使用中的过程[安装监视解决方案](../insights/solutions.md#install-a-monitoring-solution)若要安装**Activity Log Analytics**解决方案。 没有需要其他配置。

### <a name="use-the-solution"></a>使用解决方案
从访问监视解决方案**监视器**在 Azure 门户中的菜单。 选择**更多**中**Insights**部分，以打开**概述**页与解决方案磁贴。 **Azure 活动日志**磁贴显示的数的计数**AzureActivity**你的工作区中的记录。

![Azure 活动日志磁贴](media/collect-activity-logs/azure-activity-logs-tile.png)


单击**Azure 活动日志**磁贴以打开**Azure 活动日志**视图。 则视图包括下表中的可视化部件。 每个部分列出了匹配该部件的条件的指定的时间范围内的最多 10 个项。 可以运行返回所有匹配的记录，通过单击一个日志查询**查看所有**部分的底部。

![Azure 活动日志仪表板](media/collect-activity-logs/activity-log-dash.png)

| 可视化部件 | 描述 |
| --- | --- |
| Azure 活动日志条目 | 显示所选日期范围内的记录总数的条形图的最上面的 Azure 活动日志条目，并显示前 10 个活动调用方的列表。 单击该条形图可针对 `AzureActivity` 运行日志搜索。 单击某个调用方项，运行返回该项目的所有活动日志条目的日志搜索。 |
| 按状态分类的活动日志 | 显示所选的日期范围和一系列的前十个状态记录的 Azure 活动日志状态的圆环图。 单击图表以运行日志查询`AzureActivity | summarize AggregatedValue = count() by ActivityStatus`。 单击某个状态项，运行日志搜索，从而返回为该状态记录的所有活动日志条目。 |
| 按资源分类的活动日志 | 显示包含活动日志的资源的总数，并列出的前十个记录计数的每个资源。 单击全部区域可针对 `AzureActivity | summarize AggregatedValue = count() by Resource` 运行日志搜索，这会显示解决方案可以使用的所有 Azure 资源。 单击某个资源以运行返回该资源的所有活动记录的日志查询。 |
| 按资源提供程序分类的活动日志 | 显示生成活动日志的资源提供程序的总数目，并列出前 10 个。 单击要运行的一个日志查询的总区域`AzureActivity | summarize AggregatedValue = count() by ResourceProvider`，它显示了所有 Azure 资源提供程序。 单击要运行的提供程序返回所有活动记录的日志查询的资源提供程序。 |

## <a name="next-steps"></a>后续步骤

- 详细了解如何[活动日志](activity-logs-overview.md)。
- 详细了解 [Azure Monitor 数据平台](data-platform.md)。
- 使用[记录查询](../log-query/log-query-overview.md)以查看从活动日志的详细的信息。
