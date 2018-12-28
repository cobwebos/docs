---
title: Azure Log Analytics 查询中的 workspace() 表达式 | Microsoft Docs
description: workspace 表达式用于 Log Analytics 查询，以从同一资源组、另一个资源组或另一个订阅的特定工作区中检索数据。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: 24a737a728b0a249fda76cbff481bea284ac24aa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53182938"
---
# <a name="workspace-expression-in-log-analytics-query"></a>Log Analytics 查询中的 workspace() 表达式

`workspace` 表达式用于 Log Analytics 查询，以从同一资源组、另一个资源组或另一个订阅的特定工作区中检索数据。 此表达式适用于在 Log Analytics 查询中包含日志数据以及在日志查询中在多个工作区上查询数据。


## <a name="syntax"></a>语法

`workspace(`标识符`)`

## <a name="arguments"></a>参数

- 标识符：使用下表中的某种格式标识工作区。

| 标识符 | Description | 示例
|:---|:---|:---|
| 资源名称 | 人工可读的工作区名称（又称“组件名称”） | workspace("contosoretail") |
| 限定的名称 | 窗体中工作区的完整名称：“subscriptionName/resourceGroup/componentName” | workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | 工作区 GUID | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Azure 资源 ID | Azure 资源的标识符 | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>说明

* 你必须具有该工作区的读取权限。
* 相关的表达式是 `app`，可以在 Application Insights 应用程序上进行查询。

## <a name="examples"></a>示例

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>后续步骤

- 请参阅[应用表达式](workspace-expression.md)，以引用 Application Insights 应用。
- 阅读有关 [Log Analytics 数据](../../azure-monitor/log-query/log-query-overview.md)的存储方式的信息。