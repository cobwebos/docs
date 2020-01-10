---
title: 查看 Azure 资源的 RBAC 更改的活动日志 | Microsoft Docs
description: 查看过去 90 天内 Azure 资源的基于角色的访问控制 (RBAC) 更改的活动日志。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 753c626fe44193b83cbd992f225fe01c2ff67f89
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75744805"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>查看 Azure 资源的 RBAC 更改的活动日志

有时需要了解 Azure 资源的基于角色的访问控制 (RBAC) 更改，如出于审核或故障排除目的。 只要有人更改了你订阅中的角色分配或角色定义，这些更改就会被记录到 [Azure 活动日志](../azure-monitor/platform/platform-logs-overview.md)中。 可以查看活动日志，了解在过去 90 天内发生的所有 RBAC 更改。

## <a name="operations-that-are-logged"></a>记录的操作

下面是记录在活动日志中的 RBAC 相关操作：

- 创建角色分配
- 删除角色分配
- 创建或更新自定义角色定义
- 删除自定义角色定义

## <a name="azure-portal"></a>Azure 门户

最简单的入手方式就是使用 Azure 门户查看活动日志。 下面的屏幕截图展示了已筛选为显示角色分配和角色定义操作的活动日志示例。 它还包括一个用于将日志下载为 CSV 文件的链接。

![使用门户的活动日志 - 屏幕截图](./media/change-history-report/activity-log-portal.png)

门户中的活动日志有多个筛选器。 下面是与 RBAC 相关的筛选器：

|筛选  |值  |
|---------|---------|
|事件类别     | <ul><li>管理</li></ul>         |
|操作     | <ul><li>创建角色分配</li> <li>删除角色分配</li> <li>创建或更新自定义角色定义</li> <li>删除自定义角色定义</li></ul>      |


若要详细了解活动日志，请参阅[查看活动日志中的事件](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 查看活动日志，请使用 [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) 命令。

此命令列出过去 7 天内订阅中所有角色分配的更改：

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

此命令列出过去 7 天内资源组中所有角色定义的更改：

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

此命令列出过去 7 天内订阅中所有角色分配和角色定义的更改，并在列表中显示结果：

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 查看活动日志，请使用 [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) 命令。

此命令列出从启动以来资源组中存在的活动日志：

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

此命令列出从启动以来授权资源提供程序的活动日志：

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Azure Monitor 日志

[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)是另一种工具，可用于收集和分析所有 Azure 资源的 RBAC 更改。 Azure Monitor 日志具有以下优势：

- 编写复杂查询和逻辑
- 与警报、Power BI 和其他工具集成
- 以更长的保持期保存数据
- 与其他日志（例如安全性、虚拟机和自定义日志）交叉引用

以下是开始使用的基本步骤：

1. [创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。

1. 为工作区[配置 Activity Log Analytics 解决方案](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution)。

1. [查看活动日志](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution)。 导航到 "Activity Log Analytics 解决方案概述" 页的快捷方式是单击 " **Log Analytics** " 选项。

   ![门户中 Azure Monitor 日志选项](./media/change-history-report/azure-log-analytics-option.png)

1. 可以选择使用[日志搜索](../log-analytics/log-analytics-log-search.md)页面或[高级分析门户](../azure-monitor/log-query/get-started-portal.md)来查询并查看日志。 若要详细了解这两种选择，请参阅[日志搜索页面或高级分析门户](../azure-monitor/log-query/portals.md)。

以下查询返回由目标资源提供程序组织的新角色分配：

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

以下查询返回图表中显示的角色分配更改：

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationNameValue
| render timechart
```

![使用高级分析门户的活动日志 - 屏幕截图](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>后续步骤
* [在活动日志中查看事件](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [使用 Azure 活动日志监视订阅活动](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
