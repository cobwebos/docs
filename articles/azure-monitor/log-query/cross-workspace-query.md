---
title: 使用 Azure Monitor 跨资源进行查询 | Microsoft Docs
description: 本文介绍了如何在订阅中跨多个工作区以及从特定的 App Insights 应用查询资源。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 8eb163c95fb1426ebae8956d50f6d8f6aec6fd7f
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612076"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>跨工作区和应用跨 Azure Monitor 执行日志查询

Azure Monitor 日志在同一资源组、另一资源组或其他订阅中跨多个 Log Analytics 工作区和 Application Insights 应用支持查询。 这可以提供数据的系统级视图。

可以通过两种方法来查询存储在多个工作区和应用程序中的数据：
1. 通过指定工作区和应用程序详细信息来显式进行。 本文详细介绍了这一技术。
2. 使用 [资源上下文查询](../platform/design-logs-deployment.md#access-mode)隐式使用。 当你在特定资源、资源组或订阅的上下文中进行查询时，将从包含这些资源的数据的所有工作区中提取相关数据。 Application Insights 存储在应用中的数据将不会被提取。

> [!IMPORTANT]
> 如果使用的是[基于工作区的 Application Insights 资源](../app/create-workspace-resource.md)，则遥测与其他所有日志数据一起存储在 Log Analytics 工作区中。 使用 log() 表达式编写一个查询来包括多个工作区中的应用。 对于同一个工作区中的多个应用，则无需跨工作区查询。


## <a name="cross-resource-query-limits"></a>跨资源查询限制 

* 可以在单个查询中包含的 Application Insights 资源和 Log Analytics 工作区的数量限制为 100。
* 视图设计器不支持跨资源查询。 可以在 Log Analytics 中创作一个查询，将其固定到 Azure 仪表板，以[将日志查询可视化](../learn/tutorial-logs-dashboards.md)。 
* 仅当前 [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)支持日志警报中的跨资源查询。 如果使用的是旧的 Log Analytics 警报 API，则需要 [切换到当前 API](../platform/alerts-log-api-switch.md)。


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>跨 Log Analytics 工作区以及从 Application Insights 进行查询
若要在查询中引用另一个工作区，请使用 [*workspace*](./workspace-expression.md) 标识符，对于 Application Insights 中的应用，请使用 [*app*](./app-expression.md) 标识符。  

### <a name="identifying-workspace-resources"></a>标识工作区资源
以下示例演示了跨 Log Analytics 工作区进行查询以从名为 *contosoretail-it* 的工作区的 Update 表中返回记录的汇总计数。 

可以通过以下任一方式来标识工作区：

* 资源名称 - 用户可读的工作区名称，有时称为“组件名称”。 

    `workspace("contosoretail-it").Update | count`

* 限定名称 - 工作区的“全名”，由订阅名称、资源组和组件名称组成，并采用以下格式：*subscriptionName/resourceGroup/componentName*。 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >因为 Azure 订阅名称不唯一，所以此标识符可能不明确。 
    >

* 工作区 ID - 工作区 ID 是分配给每个工作区的唯一不可变标识符，表示为全局唯一标识符 (GUID)。

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure 资源 ID - Azure 定义的工作区唯一标识。 当资源名称不明确时需使用资源 ID。  对于工作区，此格式为：/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName。  

    例如：
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>标识应用程序
以下示例返回针对 Application Insights 中名为 *fabrikamapp* 的应用发出的请求的汇总计数。 

可以使用 *app(Identifier)* 表达式标识 Application Insights 中的应用程序。  *Identifier* 参数使用下列项之一来指定应用：

* 资源名称 - 人类可读的应用名称，有时称为“组件名称”。  

    `app("fabrikamapp")`

    >[!NOTE]
    >按名称标识应用程序会假设它在所有可访问的订阅中是唯一的。 如果具有多个采用指定名称的应用程序，查询将因多义性而失败。 在这种情况下，必须使用其他标识符之一。

* 限定名称 - 应用的“全名”，由订阅名称、资源组和组件名称组成，并采用以下格式：*subscriptionName/resourceGroup/componentName*。 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >因为 Azure 订阅名称不唯一，所以此标识符可能不明确。 
    >

* ID - 应用程序的应用 GUID。

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure 资源 ID - Azure 定义的应用唯一标识。 当资源名称不明确时需使用资源 ID。 格式为： */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName*。  

    例如：
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>跨多个资源执行查询
可以从任何资源实例查询多个资源，这些资源实例可以是工作区和应用程序的组合。
    
跨两个工作区进行查询的示例：    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>针对多个资源使用跨资源查询
使用跨资源查询来关联来自多个 Log Analytics 工作区和 Application Insights 资源的数据时，查询可能变得复杂且难以维护。 应利用 [Azure Monitor 日志查询中的函数](functions.md)将查询逻辑与查询资源的范围分开，这样可以简化查询结构。 以下示例演示了如何监视多个 Application Insights 资源，并按应用程序名称显示失败请求的计数。 

创建如下所示的引用 Application Insights 资源范围的查询。 `withsource= SourceApp` 命令可添加用于指定发送日志的应用程序名称的列。 使用别名 applicationsScoping[ 将查询另存为函数 ](functions.md#create-a-function)。

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



现可在跨资源查询中[使用此函数](./functions.md#use-a-function)，如下所示。 函数别名 applicationsScoping 返回来自所有已定义应用程序的请求表的并集。 然后，查询筛选失败的请求，并按应用程序显示趋势。 在此示例中，分析运算符是可选的。 该运算符从 SourceApp 属性中提取应用程序名称。

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> 此方法不能用于日志警报，因为警报规则资源（包括工作区和应用程序）的访问验证在创建警报时执行。 不支持在创建警报后将新资源添加到该函数。 若要使用函数在日志警报中限定资源范围，则需要在门户中或使用资源管理器模板来编辑警报规则，以更新范围内资源。 此外，也可以在日志警报查询中添加资源列表。


![时间表](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>后续步骤

- 查看[在 Azure Monitor 中分析日志数据](log-query-overview.md)来大致了解日志查询以及 Azure Monitor 日志数据是如何构造的。

