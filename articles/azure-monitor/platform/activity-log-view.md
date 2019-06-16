---
title: 在 Azure 监视器中查看 Azure 活动日志事件
description: 在 Azure 监视器中查看 Azure 活动日志，并使用 PowerShell、 CLI 和 REST API 检索。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 32578f77f2b3f30d80953bdd1099d22c945c640b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248109"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>查看和检索 Azure 活动日志事件

[Azure 活动日志](activity-logs-overview.md)深入了解 Azure 中发生的订阅级别事件。 本文提供有关用于查看和检索活动日志事件的不同方法的详细信息。

## <a name="azure-portal"></a>Azure 门户
查看所有资源的活动日志**监视器**在 Azure 门户中的菜单。 查看从特定资源的活动日志**活动日志**该资源的菜单中的选项。

![查看活动日志](./media/activity-logs-overview/view-activity-log.png)

您可以筛选活动日志事件按以下字段：

* **时间跨度**:事件开始和结束时间。
* **类别**：事件类别中所述[活动日志中的类别](activity-logs-overview.md#categories-in-the-activity-log)。
* **订阅**：一个或多个 Azure 订阅名称。
* **资源组**：一个或多个所选订阅中的资源组。
* **资源 （名称）** :-特定资源的名称。
* **资源类型**：资源类型，例如_microsoft.compute/virtualmachines_。
* **操作名称**-Azure 资源管理器操作，例如名称_Microsoft.SQL/servers/Write_。
* **严重性**：事件的严重级别。 可用值为_Informational_，_警告_，_错误_，_严重_。
* **事件发起者**：执行该操作的用户。
* **打开搜索**:打开搜索文本框中的所有事件的所有字段中搜索该字符串。

### <a name="view-change-history"></a>查看更改历史记录

当查看活动日志，它可以帮助以了解期间所发生的更改事件时间。 您可以查看此信息与**更改历史记录**。 选择你想要查看深入到活动日志中的事件。 选择**更改历史记录 （预览版）** 选项卡以查看任何与该事件关联的更改。

![更改事件的历史记录列表](media/activity-logs-overview/change-history-event.png)

如果有与事件相关联的任何更改，将看到可以选择的更改的列表。 此操作将打开**更改历史记录 （预览版）** 页。 此页上，请参阅对资源更改。 您可以看到从下面的示例，我们就能够不仅可看到 VM 大小，但什么以前的 VM 大小更改之前，它已更改为已更改。

![显示差异的更改历史记录页](media/activity-logs-overview/change-history-event-details.png)

若要了解有关更改历史记录的详细信息，请参阅[获取资源更改](../../governance/resource-graph/how-to/get-resource-changes.md)。


## <a name="log-analytics-workspace"></a>Log Analytics 工作区
单击**日志**顶部**活动日志**页面上以打开[Activity Log Analytics 监视解决方案](activity-log-collect.md)的订阅。 这样，您可以查看活动日志的分析并运行[记录查询](../log-query/log-query-overview.md)与**AzureActivity**表。 如果你的活动日志未连接到 Log Analytics 工作区中，将提示您执行此配置。



## <a name="powershell"></a>PowerShell
使用[Get AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet 从 PowerShell 检索活动日志。 以下是一些常见示例。

> [!NOTE]
> `Get-AzLog` 仅提供 15 天的历史记录。 使用 **-MaxEvents**参数来查询 15 天之外的最后一个 N 事件。 若要访问时间超过 15 天的事件，请使用 REST API 或 SDK。 如果不包括 **StartTime**，则默认值为 **EndTime** 减去一小时。 如果不包括 **EndTime**，则默认值为当前时间。 所有时间均是 UTC 时间。


获取特定的日期时间后创建日志条目：

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

获取一个日期时间范围的日志条目：

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

从特定资源组中获取日志条目︰

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

从日期时间范围之间的特定资源提供程序获取日志条目：

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

获取与特定的调用方的日志条目：

```powershell
Get-AzLog -Caller 'myname@company.com'
```

获取最后 1000 个事件：

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
使用[az monitor 活动日志](cli-samples.md#view-activity-log-for-a-subscription)从 CLI 中检索活动日志。 以下是一些常见示例。


查看所有可用选项。

```azurecli
az monitor activity-log list -h
```

从特定资源组中获取日志条目︰

```azurecli
az monitor activity-log list --resource-group <group name>
```

获取与特定的调用方的日志条目：

```azurecli
az monitor activity-log list --caller myname@company.com
```

获取日志的调用方对资源类型，在日期范围内：

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
使用[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/)从 REST 客户端检索活动日志。 以下是一些常见示例。

使用筛选器获取活动日志：

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

使用筛选器，选择获取活动日志：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

使用 select 获取活动日志：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

获取活动日志，而无需筛选器或选择：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>后续步骤

* [阅读有关活动日志概述](activity-logs-overview.md)
* [活动日志存档到存储或流式传输到事件中心](activity-log-export.md)
* [将 Azure 活动日志流式传输到事件中心](activity-logs-stream-event-hubs.md)
* [Azure 活动日志存档到存储](archive-activity-log.md)

