---
title: 在 Azure 中查看 RBAC 更改的活动日志 | Microsoft Docs
description: 查看过去 90 天基于角色的访问控制更改的活动日志。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e48ea2293c186bbc337f9d70464df374d64b5e61
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203897"
---
# <a name="view-activity-logs-for-role-based-access-control-changes"></a>查看基于角色的访问控制更改的活动日志

只要有人在你的订阅中对角色定义或角色分配做出了更改，这些更改都会被记录在管理类别中的 [Azure 活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)中。 可以查看活动日志，了解基于角色的访问控制 (RBAC) 在过去 90 天发生的所有更改。

## <a name="operations-that-are-logged"></a>记录的操作

下面是记录在活动日志中的 RBAC 相关操作：

- 创建或更新自定义角色定义
- 删除自定义角色定义
- 创建角色分配
- 删除角色分配

## <a name="azure-portal"></a>Azure 门户

最简单的入手方式就是使用 Azure 门户查看活动日志。 以下屏幕截图显示了一个活动日志的示例，且已将日志筛选为显示“管理”类别以及角色定义和角色分配操作。 它还包括一个能将日志下载为 CSV 文件的链接。

![使用门户的活动日志 - 屏幕截图](./media/change-history-report/activity-log-portal.png)

有关详细信息，请参阅[在活动日志中查看事件](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)。

## <a name="azure-powershell"></a>Azure PowerShell

若要使用 Azure PowerShell 查看活动日志，请使用 [Get-AzureRmLog](/powershell/module/azurerm.insights/get-azurermlog) 命令。

此命令列出过去 7 天内订阅中所有角色分配的更改：

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

此命令列出过去 7 天内资源组中所有角色定义的更改：

```azurepowershell
Get-AzureRmLog -ResourceGroupName pharma-sales-projectforecast -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

此命令列出过去 7 天内订阅中所有角色分配和角色定义的更改，并在列表中显示结果：

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
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
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast"}}

```

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 查看活动日志，请使用 [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) 命令。

此命令列出从启动以来资源组中存在的活动日志：

```azurecli
az monitor activity-log list --resource-group pharma-sales-projectforecast --start-time 2018-04-20T00:00:00Z
```

此命令列出从启动以来授权资源提供程序的活动日志：

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-log-analytics"></a>Azure Log Analytics

[Azure Log Analytics](../log-analytics/log-analytics-overview.md) 是另一种工具，可使用它针对所有 Azure 资源收集并分析基于角色的访问控制更改。 Log Analytics 有以下优势：

- 编写复杂查询和逻辑
- 与警报、Power BI 和其他工具集成
- 以更长的保持期保存数据
- 与其他日志（例如安全性、虚拟机和自定义）交叉引用

以下是开始使用的基本步骤：

1. [创建 Log Analytics 工作区](../log-analytics/log-analytics-quick-create-workspace.md)。

1. 为工作区[配置 Activity Log Analytics 解决方案](../log-analytics/log-analytics-activity.md#configuration)。

1. [查看活动日志](../log-analytics/log-analytics-activity.md#using-the-solution)。 单击“Log Analytics”选项可以快速导航至 Activity Log Analytics 概述页面。

   ![门户中的 Log Analytics 选项](./media/change-history-report/azure-log-analytics-option.png)

1. 可以选择使用[日志搜索](../log-analytics/log-analytics-log-search.md)页面或[高级分析门户](https://docs.loganalytics.io/docs/Learn)来查询并查看日志。 若要详细了解这两种选择，请参阅[日志搜索页面或高级分析门户](../log-analytics/log-analytics-log-search-portals.md)。

以下查询返回由目标资源提供程序组织的新角色分配：

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

以下查询返回图表中显示的角色分配更改：

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![使用高级分析门户的活动日志 - 屏幕截图](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>后续步骤
* [在活动日志中查看事件](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [使用 Azure 活动日志监视订阅活动](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
