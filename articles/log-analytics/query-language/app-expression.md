---
title: Azure Log Analytics 查询中的 app() 表达式 | Microsoft Docs
description: app 表达式用于 Log Analytics 查询，可从特定 Application Insights 应用中的同一资源组、另一个资源组或另一个订阅中检索数据。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: dda137ecba91e3ac59be635eed22d18e5bcadcce
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277301"
---
# <a name="app-expression-in-log-analytics-query"></a>Log Analytics 查询中的 app() 表达式

`app` 表达式用于 Log Analytics 查询，可从特定 Application Insights 应用中的同一资源组、另一个资源组或另一个订阅中检索数据。 此表达式适用于在 Log Analytics 查询中包含应用程序数据以及在 Application Insights 查询中跨多个应用程序查询数据。



## <a name="syntax"></a>语法

`app(`标识符`)`


## <a name="arguments"></a>参数

- 标识符：使用下表中的某种格式标识应用。

| 标识符 | Description | 示例
|:---|:---|:---|
| 资源名称 | 人工可读的应用名称（又称“组件名称”） | app("fabrikamapp") |
| 限定的名称 | 窗体中应用的完整名称：“subscriptionName/resourceGroup/componentName” | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID | 应用的 GUID | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure 资源 ID | Azure 资源的标识符 |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>说明

* 必须具有该应用程序的读取权限。
* 按应用程序的名称来标识应用程序，即假定该名称在所有可访问订阅中唯一。 如果拥有多个采用指定名称的应用程序，查询将因多义性而失败。 在这种情况下，必须使用一个其他的标识符。
* 使用相关表达式 [workspace](workspace-expression.md) 来跨 Log Analytics 工作区进行查询。

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

- 参阅 [workspace 表达式](workspace-expression.md)并延伸到 Log Analytics 工作区。
- 阅读有关 [Log Analytics 数据](../../log-analytics/log-analytics-queries.md)的存储方式的信息。