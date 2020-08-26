---
title: Azure Monitor 工作簿数据源 | Microsoft Docs
description: 使用从多个数据源预生成的以及自定义的参数化 Azure Monitor 工作簿简化复杂的报表
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: mbullwin
ms.openlocfilehash: f27771291d95770a693fa56041f7dce3de459d13
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081417"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor 工作簿数据源

工作簿与大量的数据源兼容。 本文详细介绍当前可用于 Azure Monitor 工作簿的数据源。

## <a name="logs"></a>日志

工作簿允许查询来自以下源的日志：

* Azure Monitor 日志（Application Insights 资源和 Log Analytics 工作区。）
* 以资源为中心的数据（活动日志）

工作簿作者可以使用用于转换基础资源数据的 KQL 查询，以选择可作为文本、图表或网格可视化的结果集。

![工作簿日志报表界面的屏幕截图](./media/workbooks-overview/logs.png)

工作簿作者可以轻松查询多个资源，以创建真正统一的丰富报表体验。

## <a name="metrics"></a>指标

Azure 资源发出可以通过工作簿访问的[指标](data-platform-metrics.md)。 可以在工作簿中通过一个专用控件访问指标，该控件可让你指定目标资源、所需指标和聚合。 然后，可以在图表或网格中绘制这些数据。

![CPU 利用率工作簿指标图表的屏幕截图](./media/workbooks-overview/metrics-graph.png)

![工作簿指标界面的屏幕截图](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

工作簿支持使用 Azure Resource Graph (ARG) 查询资源及其元数据。 此功能主要用于生成报表的自定义查询范围。 资源范围通过 ARG 支持的 KQL 子集来表示，这通常足以满足常见的用例。

若要让查询控件使用此数据源，请使用“查询类型”下拉列表选择“Azure Resource Graph”，然后选择目标订阅。 使用“查询”控件添加选择有意义资源子集的 ARG KQL 子集。

![Azure Resource Graph KQL 查询的屏幕截图](./media/workbooks-overview/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure 资源管理器

工作簿支持 Azure 资源管理器 REST 操作。 这样，便可以查询 management.azure.com 终结点，而无需提供自己的授权标头令牌。

若要使查询控件使用此数据源，请使用 "数据源" 下拉箭头选择 Azure 资源管理器。 提供相应的参数，如 Http 方法、url 路径、标头、url 参数和/或正文。

> [!NOTE]
> `GET`目前仅 `POST` 支持、和 `HEAD` 操作。

## <a name="azure-data-explorer"></a>Azure 数据资源管理器

工作簿现在支持使用强大的 [Kusto](/azure/kusto/query/index) 查询语言从 [Azure 数据资源管理器](/azure/data-explorer/)群集进行查询。   

![Kusto 查询窗口的屏幕截图](./media/workbooks-overview/data-explorer.png)

## <a name="workload-health"></a>工作负荷运行状况

Azure Monitor 提供主动监视 Windows 或 Linux 来宾操作系统可用性和性能的功能。 Azure Monitor 为关键组件及其关系、有关如何度量这些组件的运行状况的条件，以及在检测到不正常情况时由哪些组件会发出警报的设置建模。 工作簿允许用户使用这些信息创建丰富的交互式报表。

若要让查询控件使用此数据源，请使用“查询类型”下拉列表选择“工作负荷运行状况”，然后选择目标订阅、资源组或 VM 资源。 使用运行状况筛选器下拉列表，根据分析需求选择有意义的运行状况事件子集。

![警报查询的屏幕截图](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure 资源运行状况

工作簿支持获取 Azure 资源运行状况，并将其与其他数据源合并，以创建丰富的交互式运行状况报告

若要让查询控件使用此数据源，请使用“查询类型”下拉列表选择“Azure 运行状况”，然后选择目标资源。 使用运行状况筛选器下拉列表，根据分析需求选择有意义的资源问题子集。

![警报查询的屏幕截图](./media/workbooks-overview/resource-health.png)

## <a name="json"></a>JSON

JSON 提供程序允许您从静态 JSON 内容创建查询结果。 它最常用于参数创建静态值的 dropdown 参数。 简单的 JSON 数组或对象将自动转换为网格行和列。  对于更具体的行为，可以使用 "结果" 选项卡和 "JSONPath" 设置来配置列。

## <a name="alerts-preview"></a>警报（预览版）

> [!NOTE]
> 查询 Azure 警报信息的建议方法是通过查询表来使用[Azure 资源关系图](#azure-resource-graph)数据源 `AlertsManagementResources` 。
>
> 有关示例，请参阅[Azure 资源关系图表引用](../../governance/resource-graph/reference/supported-tables-resources.md)或[警报模板](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook)。
>
> 警报数据源将在一段时间内保持可用，同时作者转换为使用 ARG。 不建议在模板中使用此数据源。 

工作簿允许用户可视化与其资源相关的活动警报。 限制：警报数据源需要订阅的 "读取" 访问权限才能查询资源，可能不会显示较新的警报类型。 

若要使查询控件使用此数据源，请使用 "_数据源_" 下拉箭头选择 "_警报（预览）_ "，然后选择要面向的订阅、资源组或资源。 使用警报筛选器下拉列表根据分析需求选择一个有意义的警报子集。

## <a name="custom-endpoint"></a>自定义终结点

工作簿支持从任何外部源获取数据。 如果数据位于 Azure 外部，可以使用此数据源类型将数据引入工作簿。

若要使查询控件使用此数据源，请使用 "_数据源_" 下拉箭头选择 "_自定义终结点_"。 提供相应的参数，如 `Http method` 、 `url` 、 `headers` `url parameters` 和/或 `body` 。 请确保数据源支持[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) ，否则请求将会失败。

若要避免在使用模板时自动调用不受信任的主机，用户需要将使用的主机标记为受信任。 为此，可以单击 "_添加为受信任_" 按钮，或在 "工作簿设置" 中将其添加为受信任的主机。 这些设置将保存在支持与 web 工作进程 IndexDb 的浏览器中，有关详细信息，请参阅[此处](https://caniuse.com/#feat=indexeddb)。

> [!NOTE]
> 请勿在任何字段（ `headers` 、、、）中写入任何 `parameters` 机密 `body` `url` ，因为这些字段对所有工作簿用户均可见。

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)详细了解工作簿丰富的可视化效果选项。
* [控制](workbooks-access-control.md)并共享对工作簿资源的访问权限。
* [Log Analytics 查询优化提示](../log-query/query-optimization.md)
* 
