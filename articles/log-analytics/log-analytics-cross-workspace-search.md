---
title: "跨 Azure Log Analytics 工作区执行查询 | Microsoft Docs"
description: "本文介绍如何在订阅中跨订多个工作区进行查询，并且附有示例。"
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
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>跨多个 Log Analytics 工作区执行查询

之前通过 Azure Log Analytics，只可以分析来自当前工作区内的数据，这限制了跨多个工作区（在订阅中定义的）查询数据的能力。  

现在可跨多个工作区进行查询，以此实现在系统范围内查看数据。  只可以在[高级门户](log-analytics-log-search-portals.md#advanced-analytics-portal)中执行此类查询，而不是在 Azure 门户中。  

## <a name="querying-across-log-analytics-workspaces"></a>跨 Log Analytics 工作区查询
若要在查询中引用另一个工作区，请使用 workspace 标识符。  例如，以下查询会从当前工作区和另一个名为 contosoretail-it 工作区的 Update 表中返回更新的分类所需的这些更新汇总计数。  


## <a name="identifying-resources"></a>标识资源
可以通过以下任一方式标识工作区：

* 资源名称 - 用户可读的工作区名称，有时称为“组件名称”。 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >按工作区的名称来标识它，则会假设它在所有可访问订阅中是唯一的。 如果具有多个采用指定名称的应用程序，查询将因多义性而失败。 在这种情况下，必须使用一个其他的标识符。

* 限定名称 - 工作区的“全名”，由订阅名称、资源组和组件名称组成，并采用以下格式：subscriptionName/resourceGroup/componentName。 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >因为 Azure 订阅名称不唯一，所以此标识符可能不明确。 
    >

* 工作区 ID - 工作区 ID 是分配给每个工作区的唯一不可变标识符，表示为全局唯一标识符 (GUID)。

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Azure 资源 ID - Azure 定义的工作区唯一标识。 在资源名称不明确时请使用此 ID。  对于工作区，此格式为：/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName。  

    例如：
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>后续步骤

请参阅 [Log Analytics 日志搜索引用](https://docs.loganalytics.io/docs/Language-Reference)，了解 Log Analytics 中所有可用的查询语法选项。    