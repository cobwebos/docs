---
title: 查看 Azure Monitor 中的 Azure 活动日志事件
description: 在 Azure Monitor 中查看 Azure 活动日志并使用 PowerShell、CLI 和 REST API 进行检索。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 4ea29888d4dcf589e3e5d4dfe594f5f4bff2287e
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559972"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>查看和检索 Azure 活动日志事件

[Azure 活动日志](platform-logs-overview.md) 可以方便用户深入了解 Azure 中发生的订阅级别事件。 本文详细介绍了如何使用不同的方法来查看和检索活动日志事件。

## <a name="azure-portal"></a>Azure 门户
在 Azure 门户的“监视器”菜单中查看所有资源的活动日志。  在该资源的菜单的“活动日志”选项中查看特定资源的活动日志。 

![查看活动日志](./media/activity-logs-overview/view-activity-log.png)

可以按以下字段筛选活动日志事件：

* **时间跨度**：事件的开始时间和结束时间。
* **类别**：[活动日志中的类别](activity-log-view.md#categories-in-the-activity-log)中所述的事件类别。
* **订阅**：一个或多个 Azure 订阅名称。
* **资源组**：所选订阅中的一个或多个资源组。
* **资源(名称)** - 特定资源的名称。
* **资源类型**：资源的类型，例如 _Microsoft.Compute/virtualmachines_。
* **操作名称** - Azure 资源管理器操作的名称，例如 _Microsoft.SQL/servers/Write_。
* **严重性**：事件的严重级别。 可用值为“信息性”、“警告”、“错误”、“严重”。    
* **事件发起者**：执行了操作的用户。
* **开放搜索**：开放的文本搜索框，可在所有事件的所有字段中搜索该字符串。

## <a name="categories-in-the-activity-log"></a>活动日志中的类别
活动日志中的每个事件都有特定的类别，该类别在下表中进行了描述。 有关这些类别的架构的完整详细信息，请参阅 [Azure 活动日志事件架构](activity-log-schema.md)。 

| Category | 说明 |
|:---|:---|
| 管理 | 包含对通过资源管理器执行的所有创建、更新、删除和操作的记录。 管理事件的示例包括创建虚拟机  和删除网络安全组  。<br><br>用户或应用程序通过资源管理器所进行的每一个操作都会作为特定资源类型上的操作建模。 如果操作类型为“写入”、“删除”或“操作”，则该操作的开始、成功或失败记录都会记录在管理类别中。    管理事件还包括任何对订阅中基于角色的访问控制进行的更改。 |
| 服务运行状况 | 包含对任何发生在 Azure 中的服务运行状况事件的记录。 服务运行状况事件的一个示例是“美国东部的 SQL Azure 正处于故障时间”。  <br><br>服务运行状况事件分 6 种：需要操作、协助恢复、事件、维护、信息或安全性。       仅当订阅中存在会受事件影响的资源时，才会创建这些事件。
| 资源运行状况 | 包含 Azure 资源发生的任何资源运行状况事件的记录。 资源运行状况事件的示例是“虚拟机运行状况已更改为不可用”。 <br><br>资源运行状况事件可以表示四种运行状况之一：可用、不可用、已降级、未知。     此外，资源运行状况事件可以归类为“平台启动”或“用户启动”。   |
| 警报 | 包含 Azure 警报的激活记录。 “过去 5 分钟内，myVM 上的 CPU 百分比已超过 80%”是警报事件的示例。 |
| 自动缩放 | 包含基于在订阅中定义的任何自动缩放设置的自动缩放引擎操作相关的事件记录。 自动缩放事件的一个示例是“自动缩放纵向扩展操作失败”。  |
| 建议 | 包含 Azure 顾问提供的建议事件。 |
| 安全性 | 包含 Azure 安全中心生成的任何警报的记录。 安全事件的一个示例是“执行了可疑的双扩展名文件”。  |
| 策略 | 包含 Azure Policy 执行的所有效果操作的记录。 策略事件的示例包括审核  和拒绝  。 Policy 执行的每个操作建模为对资源执行的操作。 |

## <a name="view-change-history"></a>查看更改历史记录

查看活动日志时，可以查看在该事件时间范围内发生了哪些更改。 可以通过“更改历史记录”查看此信息。**** 从活动日志中选择一个需要深入了解的事件。 选择“更改历史记录(预览)”选项卡，查看与该事件关联的任何更改。****

![事件的更改历史记录列表](media/activity-logs-overview/change-history-event.png)

如果有任何与该事件关联的更改，则会看到一个列表，其中包含可以选择的更改。 此时会打开“更改历史记录(预览)”页。**** 在此页上，可以看到对资源的更改。 从以下示例可以看出，我们不仅能够看到 VM 更改了大小，而且能够看到更改前 VM 的大小，以及更改后的大小。

![显示了差异的更改历史记录页](media/activity-logs-overview/change-history-event-details.png)

若要了解有关更改历史记录的详细信息，请参阅[获取资源更改](../../governance/resource-graph/how-to/get-resource-changes.md)。






## <a name="powershell"></a>PowerShell
使用 [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet 从 PowerShell 检索活动日志。 下面是一些常见示例。

> [!NOTE]
> `Get-AzLog` 仅提供 15 天的历史记录。 使用 **-MaxRecord**参数可查询超过15天的最后 N 个事件。 若要访问超过 15 天的事件，请使用 REST API 或 SDK。 如果不包括 **StartTime**，则默认值为 **EndTime** 减去一小时。 如果不包括 **EndTime**，则默认值为当前时间。 所有时间均是 UTC 时间。


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
Get-AzLog -MaxRecord 1000
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


## <a name="next-steps"></a>后续步骤

* [阅读平台日志概述](platform-logs-overview.md)
* [创建诊断设置将活动日志发送到其他目标](diagnostic-settings.md)
