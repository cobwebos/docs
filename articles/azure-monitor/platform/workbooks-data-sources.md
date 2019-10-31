---
title: Azure Monitor 多个 dispate Azure 数据源中的工作簿创建交互式报表 |Microsoft 文档
description: 通过从多个数据源生成的预生成的自定义参数化 Azure Monitor 工作簿，简化复杂报表
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: dd5068da31c3aa863fc56022834a28b60ee15004
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166195"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor 工作簿数据源

工作簿与大量数据源兼容。 本文将指导你完成当前可用于 Azure Monitor 工作簿的数据源。

## <a name="logs"></a>日志

工作簿允许查询来自以下源的日志：

* Azure Monitor 日志（Application Insights 资源和 Log Analytics 工作区。）
* 以资源为中心的数据（活动日志）

工作簿作者可以使用 KQL 查询来转换基础资源数据，以选择可作为文本、图表或网格进行可视化的结果集。

![工作簿日志报表界面的屏幕截图](./media/workbooks-overview/logs.png)

工作簿作者可以轻松地跨多个资源进行查询，从而创建真正统一的丰富报告体验。

## <a name="metrics"></a>指标

Azure 资源发出可通过工作簿访问的[指标](data-platform-metrics.md)。 可以通过专用控件在工作簿中访问指标，该控件可用于指定目标资源、所需指标和聚合。 然后，可以在图表或网格中绘制这些数据。

![Cpu 利用率的工作簿指标图表的屏幕截图](./media/workbooks-overview/metrics-graph.png)

![工作簿指标界面的屏幕截图](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

工作簿支持使用 Azure 资源图（ARG）查询资源及其元数据。 此功能主要用于为报表生成自定义查询范围。 资源范围通过 ARG 支持的 KQL 子集来表示，这通常足以满足常见的用例。

若要使查询控件使用此数据源，请使用 "查询类型" 下拉选择 "Azure 资源关系图"，并选择目标订阅。 使用 "查询" 控件可添加选择一个有趣资源子集的 ARG KQL 子集。


![Azure 资源关系图 KQL 查询的屏幕截图](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>警报（预览）

工作簿允许用户直观显示与资源相关的活动警报。 此功能允许创建将通知数据（警报）和诊断信息（指标、日志）汇集在一个报表中的报表。 此信息还可以联接在一起，以创建在这些数据源中合并见解的丰富报表。

若要使查询控件使用此数据源，请使用 "查询类型" 下拉选择 "警报"，然后选择要面向的订阅、资源组或资源。 使用 "警报筛选器" 下拉部分为分析需求选择一个有趣的警报子集。

![警报查询的屏幕截图](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>工作负荷运行状况（预览版）

Azure Monitor 具有主动监视 Windows 或 Linux 来宾操作系统的可用性和性能的功能。 Azure Monitor 对关键组件及其关系进行建模，如何测量这些组件的运行状况的条件，以及在检测到不正常情况时，哪些组件会发出警报。 工作簿允许用户使用此信息创建丰富的交互式报表。

若要使查询控件使用此数据源，请使用 "**查询类型**" 下拉选择 "工作负载运行状况"，并选择 "订阅"、"资源组" 或 "要面向的 VM 资源"。 使用 "运行状况筛选器" 下拉部分为分析需求选择一个有趣的运行状况事件子集。

![警报查询的屏幕截图](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure 资源运行状况 

工作簿支持获取 Azure 资源运行状况，并将其与其他数据源组合在一起，以创建丰富的交互式运行状况报告

若要使查询控件使用此数据源，请使用 "**查询类型**" 下拉选择 "Azure 运行状况"，并选择要作为目标的资源。 使用 "运行状况筛选器" 下拉中心为分析需求选择一小部分的资源问题。

![警报查询的屏幕截图](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure 数据资源管理器（预览版）

工作簿现在支持从[Azure 数据资源管理器](https://docs.microsoft.com/azure/data-explorer/)群集使用强大的[Kusto](https://docs.microsoft.com/azure/kusto/query/index)查询语言进行查询。   

![Kusto 查询窗口的屏幕截图](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿许多丰富可视化效果选项的详细信息。
* [控制](workbooks-access-control.md)和共享对工作簿资源的访问权限。
