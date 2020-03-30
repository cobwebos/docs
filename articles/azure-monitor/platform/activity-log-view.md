---
title: 查看 Azure Monitor 中的 Azure 活动日志事件
description: 在 Azure Monitor 中查看 Azure 活动日志并使用 PowerShell、CLI 和 REST API 进行检索。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 9df7593a9fd191d3a734fba5e81fb1aecba08345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275043"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>查看和检索 Azure 活动日志事件

[Azure 活动日志](platform-logs-overview.md) 可以方便用户深入了解 Azure 中发生的订阅级别事件。 本文详细介绍了如何使用不同的方法来查看和检索活动日志事件。

## <a name="azure-portal"></a>Azure 门户
在 Azure 门户的“监视器”菜单中查看所有资源的活动日志。**** 在该资源的菜单的“活动日志”选项中查看特定资源的活动日志。****

![查看活动日志](./media/activity-logs-overview/view-activity-log.png)

可以按以下字段筛选活动日志事件：

* **时间跨度**：事件的开始和结束时间。
* **类别**：[活动日志 中的类别中](activity-log-view.md#categories-in-the-activity-log)所述的事件类别。
* **订阅**：一个或多个 Azure 订阅名称。
* **资源组**：所选订阅中的一个或多个资源组。
* **资源（名称）：**- 特定资源的名称。
* **资源类型**：资源类型，例如_Microsoft.计算/虚拟机_。
* **操作名称**- Azure 资源管理器操作的名称，例如_Microsoft.SQL/服务器/写入_。
* **严重性**：事件的严重性级别。 可用值为“信息性”、“警告”、“错误”、“严重”。________
* **由 启动的事件 ：** 执行操作的用户。
* **打开搜索**：打开文本搜索框，该框在所有事件中的所有字段中搜索该字符串。

## <a name="categories-in-the-activity-log"></a>活动日志中的类别
活动日志中的每个事件都有特定的类别，该类别在下表中进行了描述。 有关这些类别的架构的完整详细信息，请参阅 [Azure 活动日志事件架构](activity-log-schema.md)。 

| 类别 | 描述 |
|:---|:---|
| 管理 | 包含对通过资源管理器执行的所有创建、更新、删除和操作的记录。 管理事件的示例包括创建虚拟机__ 和删除网络安全组__。<br><br>用户或应用程序通过资源管理器所进行的每一个操作都会作为特定资源类型上的操作建模。 如果操作类型为 _"写入_"、_删除_或_操作_，则该操作的开始和成功或失败的记录将记录在"管理"类别中。 管理事件还包括任何对订阅中基于角色的访问控制进行的更改。 |
| 服务运行状况 | 包含对任何发生在 Azure 中的服务运行状况事件的记录。 服务运行状况事件的一个示例是“美国东部的 SQL Azure 正处于故障时间”。__ <br><br>服务健康事件有六个品种：_行动要求_，_辅助恢复_，_事件_，_维护_，_信息_，或_安全_。 仅当订阅中存在会受事件影响的资源时，才会创建这些事件。
| 资源运行状况 | 包含 Azure 资源发生的任何资源运行状况事件的记录。 资源运行状况事件的示例是“虚拟机运行状况已更改为不可用”。__<br><br>资源运行状况事件可以表示四种运行状况之一：_可用_、_不可用_、_降级_和_未知_。 此外，资源运行状况事件可以归类为_平台启动_或_用户启动_。 |
| 警报 | 包含 Azure 警报的激活记录。 “过去 5 分钟内，myVM 上的 CPU 百分比已超过 80%”是警报事件的示例。__|
| 自动缩放 | 包含基于在订阅中定义的任何自动缩放设置的自动缩放引擎操作相关的事件记录。 自动缩放事件的一个示例是“自动缩放纵向扩展操作失败”。__ |
| 建议 | 包含 Azure 顾问提供的建议事件。 |
| 安全性 | 包含 Azure 安全中心生成的任何警报的记录。 安全事件的一个示例是“执行了可疑的双扩展名文件”。__ |
| 策略 | 包含 Azure Policy 执行的所有效果操作的记录。 策略事件的示例包括审核__ 和拒绝__。 Policy 执行的每个操作建模为对资源执行的操作。 |

## <a name="view-change-history"></a>查看更改历史记录

查看活动日志时，可以查看在该事件时间范围内发生了哪些更改。 可以通过“更改历史记录”查看此信息。**** 从活动日志中选择一个需要深入了解的事件。 选择“更改历史记录(预览)”选项卡，查看与该事件关联的任何更改。****

![事件的更改历史记录列表](media/activity-logs-overview/change-history-event.png)

如果有任何与该事件关联的更改，则会看到一个列表，其中包含可以选择的更改。 此时会打开“更改历史记录(预览)”页。**** 在此页上，可以看到对资源的更改。 从以下示例可以看出，我们不仅能够看到 VM 更改了大小，而且能够看到更改前 VM 的大小，以及更改后的大小。

![显示了差异的更改历史记录页](media/activity-logs-overview/change-history-event-details.png)

要了解有关更改历史记录的更多信息，请参阅[获取资源更改](../../governance/resource-graph/how-to/get-resource-changes.md)。






## <a name="powershell"></a>PowerShell
使用 [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet 从 PowerShell 检索活动日志。 下面是一些常见示例。

> [!NOTE]
> `Get-AzLog` 仅提供 15 天的历史记录。 使用 **-MaxEvents** 参数查询 15 天之外的最后 N 个事件。 若要访问超过 15 天的事件，请使用 REST API 或 SDK。 如果不包括 **StartTime**，则默认值为 **EndTime** 减去一小时。 如果不包括 **EndTime**，则默认值为当前时间。 所有时间均是 UTC 时间。


获取在特定日期时间之后创建的日志条目：

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

在一个日期时间范围中获取日志条目：

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

从特定资源组中获取日志条目︰

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

在一个日期时间范围中从特定资源提供程序获取日志条目：

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

获取特定调用方的日志条目：

```powershell
Get-AzLog -Caller 'myname@company.com'
```

获取最后 1000 个事件：

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
使用 [az monitor activity-log](cli-samples.md#view-activity-log-for-a-subscription) 从 CLI 检索活动日志。 下面是一些常见示例。


查看所有可用选项。

```azurecli
az monitor activity-log list -h
```

从特定资源组中获取日志条目︰

```azurecli
az monitor activity-log list --resource-group <group name>
```

获取特定调用方的日志条目：

```azurecli
az monitor activity-log list --caller myname@company.com
```

在日期范围内，按调用方获取某个资源类型的日志：

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
使用 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) 从 REST 客户端检索活动日志。 下面是一些常见示例。

使用筛选器获取活动日志：

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

使用筛选器和 select 获取活动日志：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

使用 select 获取活动日志：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

不使用筛选器或 select 获取活动日志：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="activity-logs-analytics-monitoring-solution"></a>Activity Logs Analytics 监视解决方案
Azure Log Analytics 监视解决方案包含多个日志查询和视图，用于分析 Log Analytics 工作区中的活动日志记录。

### <a name="prerequisites"></a>先决条件
必须创建一个诊断设置，以便将订阅的活动日志发送到 Log Analytics 工作区。 请参阅[在 Azure Monitor 的 Log Analytics 工作区中收集 Azure 平台日志](resource-logs-collect-workspace.md)。

### <a name="install-the-solution"></a>安装解决方案
按照[监视解决方案](../insights/solutions.md#install-a-monitoring-solution)中的过程安装 **Activity Log Analytics** 解决方案。 无需其他配置。

### <a name="use-the-solution"></a>使用解决方案
单击“活动日志”页顶部的“日志”，打开订阅的 [Activity Log Analytics 监视解决方案](activity-log-collect.md)。******** 或在 Azure 门户的订阅“监视”**** 菜单中访问所有监视解决方案。 在“见解”部分选择“更多”，打开包含解决方案磁贴的“概览”页************。 “Azure 活动日志”磁贴显示工作区中 **AzureActivity** 记录的计数。****

![Azure 活动日志磁贴](media/collect-activity-logs/azure-activity-logs-tile.png)


单击“Azure 活动日志”**** 磁贴，打开“Azure 活动日志”**** 视图。 视图包含下表中的可视化部件。 每个部件按照指定时间范围列出了匹配该部件条件的最多 10 个项。 可通过单击部件底部的“查看全部”**** 运行返回所有匹配记录的日志查询。

![Azure 活动日志仪表板](media/collect-activity-logs/activity-log-dash.png)

| 可视化部件 | 描述 |
| --- | --- |
| Azure 活动日志条目 | 显示所选日期范围内排名前列的 Azure 活动日志条目记录总数的条形图，并显示前 10 个活动调用方的列表。 单击该条形图可针对 `AzureActivity` 运行日志搜索。 单击某个调用方项，运行日志搜索，为该项返回所有活动日志条目。 |
| 按状态分类的活动日志 | 为所选日期范围内的 Azure 活动日志状态显示圆环图，并显示一个包含前十个状态记录的列表。 单击该图表可针对 `AzureActivity | summarize AggregatedValue = count() by ActivityStatus` 运行日志查询。 单击某个状态项，运行日志搜索，为该状态记录返回所有活动日志条目。 |
| 按资源分类的活动日志 | 显示包含活动日志的资源总数，并列出前十个为每个资源显示记录计数的资源。 单击全部区域可针对 `AzureActivity | summarize AggregatedValue = count() by Resource` 运行日志搜索，这会显示解决方案可以使用的所有 Azure 资源。 单击某个资源以运行日志查询，为该资源返回所有活动记录。 |
| 按资源提供程序分类的活动日志 | 显示生成活动日志的资源提供程序的总数，并列出前十个资源提供程序。 单击总区域可针对 `AzureActivity | summarize AggregatedValue = count() by ResourceProvider` 运行日志查询，这会显示所有 Azure 资源提供程序。 单击某个资源提供程序可以运行日志查询，为该提供程序返回所有活动记录。 |




## <a name="next-steps"></a>后续步骤

* [阅读平台日志概述](platform-logs-overview.md)
* [创建诊断设置将活动日志发送到其他目标](diagnostic-settings.md)
