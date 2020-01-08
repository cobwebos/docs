---
title: Azure Monitor 日志查询中的 workspace() 表达式 | Microsoft Docs
description: workspace 表达式用于 Azure Monitor 日志查询中，以从同一资源组、另一个资源组或另一个订阅的特定工作区中检索数据。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 255888acf5da6149b6a964b23ed038b99715481c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364945"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>Azure Monitor 日志查询中的 workspace() 表达式

`workspace` 表达式用于 Azure Monitor 查询中，以从同一资源组、另一个资源组或另一个订阅的特定工作区中检索数据。 此表达式适用于在 Log Analytics 查询中包含日志数据以及在日志查询中在多个工作区上查询数据。


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

- 请参阅[应用表达式](app-expression.md)以引用 Application Insights 应用。
- 了解 [Azure Monitor 数据是如何存储的](log-query-overview.md)。
- 访问有关 [Kusto 查询语言](/azure/kusto/query/)的完整文档。
