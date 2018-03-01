---
title: "跨 Azure Log Analytics 工作区执行查询 | Microsoft Docs"
description: "本文介绍了如何在订阅中跨多个工作区以及从特定的 App Insights 应用进行查询。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: magoedte
ms.openlocfilehash: 403448995c28ff7172d2c3abbf3b9d67341017b4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-perform-queries-across-multiple-log-analytics-workspaces"></a>如何跨多个 Log Analytics 工作区执行查询

之前通过 Azure Log Analytics，只可以分析来自当前工作区内的数据，这限制了跨多个工作区（在订阅中定义的）查询数据的能力。  

现在不但可以跨多个 Log Analytics 工作区进行查询，而且还可以查询同一资源组、另一资源组或另一订阅中特定 Application Insights 应用的数据。 这可以提供数据的系统级视图。  只可以在[高级门户](log-analytics-log-search-portals.md#advanced-analytics-portal)中执行此类查询，而不是在 Azure 门户中。  

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>跨 Log Analytics 工作区以及从 Application Insights 进行查询
若要在查询中引用另一个工作区，请使用 [*workspace*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()) 标识符，对于 Application Insights 中的应用，请使用 [*app*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app()) 标识符。  

例如，第一个查询会从当前工作区和另一个名为 contosoretail-it 工作区的 Update 表中返回更新的分类所需的这些更新汇总计数。  第二个查询示例返回针对 Application Insights 中名为 *fabrikamapp* 的应用发出的请求的汇总计数。 

### <a name="identifying-workspace-resources"></a>标识工作区资源
可以通过以下任一方式标识工作区：

* 资源名称 - 用户可读的工作区名称，有时称为“组件名称”。 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >按工作区的名称来标识它，则会假设它在所有可访问订阅中是唯一的。 如果具有多个采用指定名称的应用程序，查询将因多义性而失败。 在这种情况下，必须使用其他标识符之一。

* 限定名称 - 工作区的“全名”，由订阅名称、资源组和组件名称组成，并采用以下格式：*subscriptionName/resourceGroup/componentName*。 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >因为 Azure 订阅名称不唯一，所以此标识符可能不明确。 
    >

* 工作区 ID - 工作区 ID 是分配给每个工作区的唯一不可变标识符，表示为全局唯一标识符 (GUID)。

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Azure 资源 ID - Azure 定义的工作区唯一标识。 当资源名称不明确时需使用资源 ID。  对于工作区，此格式为：/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName。  

    例如：
    ``` 
    workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

### <a name="identifying-an-application"></a>标识应用程序

可以使用 *app(Identifier)* 表达式标识 Application Insights 中的应用程序。  *Identifier* 参数使用下列项之一来指定应用：

* 资源名称 - 人类可读的应用名称，有时称为“组件名称”。  

    `app("fabrikamapp")`

* 限定名称 - 应用的“全名”，由订阅名称、资源组和组件名称组成，并采用以下格式：*subscriptionName/resourceGroup/componentName*。 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >因为 Azure 订阅名称不唯一，所以此标识符可能不明确。 
    >

* ID - 应用程序的应用 GUID。

    `app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count`

* Azure 资源 ID - Azure 定义的应用唯一标识。 当资源名称不明确时需使用资源 ID。 格式为：*/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName*。  

    例如：
    ```
    app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

## <a name="next-steps"></a>后续步骤

请参阅 [Log Analytics 日志搜索引用](https://docs.loganalytics.io/docs/Language-Reference)，了解 Log Analytics 中所有可用的查询语法选项。    