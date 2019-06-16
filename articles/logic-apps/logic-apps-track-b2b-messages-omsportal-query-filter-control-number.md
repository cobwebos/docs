---
title: 在 Azure Monitor 日志-Azure 逻辑应用中创建跟踪查询 B2B 消息 |Microsoft Docs
description: 使用 Azure Log Analytics 为 Azure 逻辑应用创建用于跟踪 AS2、X12 和 EDIFACT 消息的查询
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: d4a94e75de34bbafd3bc8f1c1a0d1a6817245e5f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60846440"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>为 Azure 逻辑应用在 Azure Monitor 日志中创建 B2B 消息的跟踪查询

若要查找 AS2，X12 或 EDIFACT 消息与跟踪[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)，可以创建筛选器操作根据特定条件的查询。 例如，你可以根据特定的交换控制编号查找消息。

> [!NOTE]
> 此页面之前描述了如何使用 Microsoft Operations Management Suite (OMS) 执行这些任务的步骤，该解决方案将[在 2019 年 1 月停用](../azure-monitor/platform/oms-portal-transition.md)，取而代之的将是使用 Azure Log Analytics 执行这些步骤的内容。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>必备组件

* 已设置诊断日志记录的逻辑应用。 了解[如何创建逻辑应用](quickstart-create-first-logic-app-workflow.md)以及[如何为逻辑应用设置日志记录](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

* 已设置监视和日志记录的集成帐户。 了解[如何创建集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)以及[如何为集成帐户设置监视和日志记录](../logic-apps/logic-apps-monitor-b2b-message.md)。

* 如果你尚未准备好，[诊断数据发布到 Azure Monitor 日志](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)并[设置消息在 Azure Monitor 日志中跟踪](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。

## <a name="create-queries-with-filters"></a>创建使用筛选器的查询

若要根据特定的属性或值查找消息，可以创建使用筛选器的查询。 

1. 在 [Azure 门户](https://portal.azure.com)中，选择“所有服务”。  在搜索框中查找“log analytics”，并选择“Log Analytics”  。

   ![选择“Log Analytics”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. 在“Log Analytics”下  ，查找并选择你的 Log Analytics 工作区。 

   ![选择 Log Analytics 工作区](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. 在工作区菜单中的“常规”下，选择“日志(经典)”或“日志”。    

   本示例演示如何使用经典日志视图。 
   如果在“最大化 Log Analytics 体验”部分的“搜索和分析日志”下选择了“查看日志”，则会显示“日志(经典视图)”。     

   ![查看经典日志](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. 在查询编辑框中，开始键入想要查找的字段名称。 开始键入时，查询编辑器将显示可能的匹配项和可用的操作。 创建查询后，选择“运行”或按 Enter 键。 

   本示例在 **LogicAppB2B** 中搜索匹配项。 
   详细了解如何[如何在 Azure Monitor 日志中查找数据](../log-analytics/log-analytics-log-searches.md)。

   ![开始键入查询字符串](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. 若要更改想要查看的时间范围，请在左窗格中，从持续时间列表中进行选择或拖动滑块。 

   ![更改时间范围](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. 若要将筛选器添加到查询，请选择“添加”。  

   ![向查询添加筛选器](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. 在“添加筛选器”下，输入想要查找的筛选器名称。  如果找到了该筛选器，请选择它。 在左窗格中，再次选择“添加”。 

   例如，下面是一个不同的查询，它会搜索 **Type=="AzureDiagnostics"** 事件，并通过选择 **event_record_messageProperties_interchangeControlNumber_s** 筛选器，根据交换控制编号来查找结果。

   ![选择筛选器值](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   选择“添加”后，将使用选定的筛选器事件和值更新查询。  
   以前的结果现在也已进行了筛选。 

   例如，此查询会搜索 **Type=="AzureDiagnostics"** ，并使用 **event_record_messageProperties_interchangeControlNumber_s** 筛选器，根据交换控制编号来查找结果。

   ![筛选的结果](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>保存查询

若要在“日志(经典)”视图中保存查询，请执行以下步骤： 

1. 在“日志(经典)”页上的查询中，选择“分析”。   

   ![选择“分析”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. 在查询工具栏上选择“保存”  。

   ![选择“保存”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. 提供有关查询的详细信息，例如，为查询指定名称，选择“查询”，并提供类别名称。  完成后，选择“保存”  。

   ![选择“保存”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. 若要查看已保存的查询，请返回到查询页。 在查询工具栏上选择“保存的搜索”  。

   ![选择“保存的搜索”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. 在“保存的搜索”下选择自己的查询，以便查看结果。  

   ![选择查询](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   若要更新查询以便找到不同的结果，请编辑查询。

## <a name="find-and-run-saved-queries"></a>查找和运行已保存的查询

1. 在 [Azure 门户](https://portal.azure.com)中，选择“所有服务”。  在搜索框中查找“log analytics”，并选择“Log Analytics”  。

   ![选择“Log Analytics”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. 在“Log Analytics”下  ，查找并选择你的 Log Analytics 工作区。 

   ![选择 Log Analytics 工作区](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. 在工作区菜单中的“常规”下，选择“日志(经典)”或“日志”。    

   本示例演示如何使用经典日志视图。 

1. 查询页打开之后，在查询工具栏上选择“保存的搜索”。 

   ![选择“保存的搜索”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. 在“保存的搜索”下选择自己的查询，以便查看结果。  

   ![选择查询](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   该查询将自动运行，但如果查询出于任何原因无法运行，请在查询编辑器中选择“运行”。 

## <a name="next-steps"></a>后续步骤

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)