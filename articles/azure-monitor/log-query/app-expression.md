---
title: Azure Monitor 日志查询中的 app() 表达式 | Microsoft Docs
description: app 表达式在 Azure Monitor 日志查询中用于从同一资源组、另一资源组或另一订阅中的特定 Application Insights 应用检索数据。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2019
ms.openlocfilehash: 5d31c829487400f8eb239c0b837e53eecafeb900
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201106"
---
# <a name="app-expression-in-azure-monitor-query"></a>Azure Monitor 查询中的 app() 表达式

`app` 表达式用于 Azure Monitor 查询中，可从特定 Application Insights 应用中的同一资源组、另一个资源组或另一个订阅中检索数据。 此表达式适用于在 Azure Monitor 日志查询中包含应用程序数据以及在 Application Insights 查询中跨多个应用程序查询数据。

> [!IMPORTANT]
> 如果使用的是[基于工作区的 Application Insights 资源](../app/create-workspace-resource.md)，则不使用 app() 表达式，因为日志数据存储在 Log Analytics 工作区中。 使用 log() 表达式可编写包括多个工作区中的应用程序的查询。 对于同一工作区中的多个应用程序，无需跨工作区查询。

## <a name="syntax"></a>语法

`app(`标识符`)`


## <a name="arguments"></a>参数

- 标识符：使用下表中的某种格式标识应用。

| 标识符 | 说明 | 示例
|:---|:---|:---|
| 资源名称 | 人工可读的应用名称（也称为“组件名称”） | app("fabrikamapp") |
| 限定的名称 | 窗体中应用的完整名称：“subscriptionName/resourceGroup/componentName” | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID | 应用的 GUID | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure 资源 ID | Azure 资源的标识符 |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>说明

* 必须具有该应用程序的读取权限。
* 按应用程序的名称来标识应用程序，即假定该名称在所有可访问订阅中唯一。 如果拥有多个采用指定名称的应用程序，查询将因多义性而失败。 在这种情况下，必须使用一个其他的标识符。
* 使用相关表达式 [workspace](workspace-expression.md) 来跨 Log Analytics 工作区进行查询。
* 除非使用 Application Insights 应用程序作为警报规则的资源，否则在使用 Azure 门户创建[自定义日志搜索警报规则](../platform/alerts-log.md)时，搜索查询目前不支持 app() 表达式。

## <a name="examples"></a>示例

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- 参阅 [workspace 表达式](workspace-expression.md)，以引用 Log Analytics 工作区。
- 了解 [Azure Monitor 数据是如何存储的](../../azure-monitor/log-query/log-query-overview.md)。
- 访问有关 [Kusto 查询语言](/azure/kusto/query/)的完整文档。