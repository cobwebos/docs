---
title: Azure Application Insights | Microsoft Docs
description: ''
services: application-insights
documentationcenter: ''
author: eternovsky
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.author: Evgeny.Ternovsky
ms.openlocfilehash: 31e37efc1aad3d355bdd8391535f317ec137f5d7
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161599"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>将 Application Insights 数据与自定义数据源相关联

Application Insights 收集多种不同的数据类型：异常、跟踪、页面视图等。 虽然通常情况下这对调查应用程序的性能、可靠性和使用情况来说已足够，但有时候，将存储在 Application Insights 中的数据与其他完全自定义的数据集相关联会很有用。

下述情况可能需要自定义数据：

- 数据扩充或查找表：例如，在提供服务器名称的同时，补充提供服务器的所有者以及服务器所在的实验室位置 
- 与非 Application Insights 数据源相关联：例如，将网店购买数据与购买履行服务的信息相关联，确定寄送时间估算的准确度如何 
- 完全自定义数据：我们的许多客户喜欢支持 Application Insights 的 Log Analytics 数据平台的查询语言和性能，希望使用它来查询与 Application Insights 根本不相关的数据。 例如，在进行智能家居安装的过程中根据[此处]( http://blogs.catapultsystems.com/cfuller/archive/2017/10/04/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)所述跟踪太阳能板的性能。

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>如何将自定义数据与 Application Insights 数据相关联 

由于 Application Insights 受强大的 Log Analytics 数据平台的支持，因此我们可以充分利用 Log Analytics 来引入数据。 然后，我们会使用“join”运算符来编写查询，将此自定义数据与 Log Analytics 中提供的数据相关联。 

## <a name="ingesting-data"></a>引入数据

在此部分，我们会讨论如何将数据引入 Log Analytics 中。

如果还没有 Log Analytics 工作区，请预配一个新的，只需根据[这些说明]( https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)操作并包括“创建工作区”步骤即可。

开始将数据发送到 Log Analytics 中。 存在多个选项：

- 对于同步机制，可以直接调用[数据收集器 API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)，也可以使用逻辑应用连接器 - 只需查找“Azure Log Analytics”并选取“发送数据”选项即可：

 ![“选择操作”的屏幕截图](./media/app-insights-custom-data-correlation/01-logic-app-connector.png)  

- 对于异步选项，请使用数据收集器 API 来生成处理管道。 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api)。

## <a name="correlating-data"></a>关联数据

Application Insights 基于 Log Analytics 数据平台。 因此，我们可以使用[跨资源联接](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-cross-workspace-search)将已引入 Log Analytics 中的任何数据与 Application Insights 数据相关联。

例如，可以将实验室库存和位置引入名为“myLA”的 Log Analytics 工作区的名为“LabLocations_CL”的表中。 然后，如果我们需要查看在名为“myAI”的 Application Insights 应用中跟踪的请求，并将处理请求的计算机名称与存储在前述自定义表中的这些计算机的位置相关联，则可在 Application Insights 或 Log Analytics 上下文中运行以下查询：

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>后续步骤

- 查看[数据收集器 API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) 参考。
- 有关[跨资源联接](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-cross-workspace-search)的详细信息。
