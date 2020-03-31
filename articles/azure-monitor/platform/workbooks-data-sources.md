---
title: Azure 监视器工作簿数据源 |微软文档
description: 使用从多个数据源构建的预构建和自定义参数化 Azure 监视器工作簿简化复杂报告
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248575"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure 监视工作簿数据源

工作簿与大量数据源兼容。 本文将引导您浏览当前可用于 Azure 监视器工作簿的数据源。

## <a name="logs"></a>日志

工作簿允许查询来自以下来源的日志：

* Azure 监视器日志（应用程序见解资源和日志分析工作区）。
* 以资源为中心的数据（活动日志）

工作簿作者可以使用转换基础资源数据的 KQL 查询来选择可以可视化为文本、图表或网格的结果集。

![工作簿日志报告界面的屏幕截图](./media/workbooks-overview/logs.png)

工作簿作者可以轻松地跨多个资源查询，从而创建真正统一的丰富报告体验。

## <a name="metrics"></a>指标

Azure 资源发出可通过工作簿访问的[指标](data-platform-metrics.md)。 指标可以通过专用控件在工作簿中访问，该控件允许您指定目标资源、所需指标及其聚合。 然后，可以在图表或网格中绘制此数据。

![工作簿 cpu 利用率指标图表的屏幕截图](./media/workbooks-overview/metrics-graph.png)

![工作簿指标界面的屏幕截图](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

工作簿支持使用 Azure 资源图 （ARG） 查询资源及其元数据。 此功能主要用于为报表生成自定义查询作用域。 资源范围通过 ARG 支持的 KQL 子集表示 - 通常对于常见用例来说，该子集就足够了。

要使查询控件使用此数据源，请使用"查询类型下拉列表"选择 Azure 资源图并选择要目标的订阅。 使用查询控件添加选择有趣资源子集的 ARG KQL 子集。


![Azure 资源图 KQL 查询的屏幕截图](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>警报（预览）

工作簿允许用户可视化与其资源相关的活动警报。 此功能允许创建将通知数据（警报）和诊断信息（指标、日志）合并到一个报表中的报表。 还可以将此信息合并在一起，以创建丰富的报告，这些报表将见解合并到这些数据源中。

要使查询控件使用此数据源，请使用"查询类型下拉列表"选择"警报"并选择要定位的订阅、资源组或资源。 使用警报筛选器下拉列表可根据您的分析需求选择一个有趣的警报子集。

![警报查询的屏幕截图](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>工作负载运行状况（预览）

Azure 监视器具有主动监视 Windows 或 Linux 来宾操作系统的可用性和性能的功能。 Azure 监视器可建模关键组件及其关系、如何测量这些组件运行状况的条件，以及检测到不正常情况时提醒您哪些组件。 工作簿允许用户使用此信息创建丰富的交互式报表。

要使查询控件使用此数据源，请使用 **"查询类型**下拉列表"选择工作负载运行状况，并选择要定位的订阅、资源组或 VM 资源。 使用运行状况筛选器下拉列表可根据您的分析需求选择一个有趣的运行状况事件子集。

![警报查询的屏幕截图](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure 资源运行状况 

工作簿支持获取 Azure 资源运行状况并将其与其他数据源结合，以创建丰富的交互式运行状况报告

要使查询控件使用此数据源，请使用 **"查询类型**下拉列表"选择 Azure 运行状况并选择要定位的资源。 使用运行状况筛选器下拉列表为分析需求选择有趣的资源问题子集。

![警报查询的屏幕截图](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure 数据资源管理器（预览）

工作簿现在支持使用强大的[Kusto](https://docs.microsoft.com/azure/kusto/query/index)查询语言从[Azure 数据资源管理器](https://docs.microsoft.com/azure/data-explorer/)群集进行查询。   

![库斯托查询窗口的屏幕截图](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿的许多丰富可视化选项的详细信息。
* [控制和](workbooks-access-control.md)共享对工作簿资源的访问权限。
