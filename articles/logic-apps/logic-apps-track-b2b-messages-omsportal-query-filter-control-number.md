---
title: "在 Operations Management Suite 门户中查询 B2B 消息 - Azure 逻辑应用 | Microsoft Docs"
description: "创建查询，以便在 Operations Management Suite 中跟踪 AS2、X12 和 EDIFACT 消息"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 2748d3d3daf7c13dca05f663a4a088598e1b3605
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="query-for-as2-x12-and-edifact-messages-in-the-microsoft-operations-management-suite-oms"></a>在 Microsoft Operations Management Suite (OMS) 中查询 AS2、X12 和 EDIFACT 消息

要在 [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中查找使用 [Azure Log Analytics](../log-analytics/log-analytics-overview.md) 跟踪的 AS2、X12 或 EDIFACT 消息，可以创建基于具体情况筛选操作的查询。 例如，你可以根据特定的交换控制编号查找消息。

## <a name="requirements"></a>要求

* 已设置诊断日志记录的逻辑应用。 了解[如何创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)以及[如何为逻辑应用设置日志记录](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

* 已设置监视和日志记录的集成帐户。 了解[如何创建集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)以及[如何为该帐户设置监视和日志记录](../logic-apps/logic-apps-monitor-b2b-message.md)。

* 如果尚未准备好，则[将诊断数据发布到 Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)并[在 OMS 中设置消息跟踪](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。

> [!NOTE]
> 在满足此前的要求后，在 [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中应该已经有一个工作区了。 应使用相同的 OMS 工作区来跟踪你在 OMS 上的 B2B 通信。 
>  
> 如果没有 OMS 工作区，了解[如何创建 OMS 工作区](../log-analytics/log-analytics-get-started.md)。

## <a name="create-message-queries-with-filters-in-the-operations-management-suite-portal"></a>在 Operations Management Suite 门户中使用筛选器创建消息查询

本示例演示如何根据交换控制编号查找消息。

> [!TIP] 
> 如果你知道自己的 OMS 工作区名称，请转到工作区主页 (`https://{your-workspace-name}.portal.mms.microsoft.com`)，然后从步骤 4 开始操作。 否则，从步骤 1 开始。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“更多服务”。 搜索“日志分析”，然后选择“Log Analytics”，如下所示：

   ![查找 Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. 在“Log Analytics”下，查找并选择 OMS 工作区。

   ![选择你的 OMS 工作区](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. 在“管理”下，选择“OMS 门户”。

   ![选择 OMS 门户](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. 在 OMS 主页上，选择“日志搜索”。

   ![在 OMS 主页上，选择“日志搜索”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -或-

   ![在 OMS 菜单上，选择“日志搜索”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. 在搜索框中，输入要查找的字段，然后按下 Enter 键。 开始输入时，OMS 会显示你可以使用的可能的匹配和操作。 详细了解[如何在 Log Analytics 中查找数据](../log-analytics/log-analytics-log-searches.md)。

   本示例搜索 Type=AzureDiagnostics 的事件。

   ![开始键入查询字符串](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. 在左侧栏中，选择要查看的时间范围。 若要向查询添加筛选器，选择“+添加”。

   ![向查询添加筛选器](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. 在“添加筛选器”下，输入筛选器名称，以便查找所需的筛选器。 选择筛选器，然后选择“+添加”。

   要查找交换控制编号，本示例搜索“interchange”一词，并选择“event_record_messageProperties_interchangeControlNumber_s”作为筛选器。

   ![选择筛选器](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. 在左侧栏中，选择要使用的筛选器值，然后选择“应用”。

   本示例中，选择所需消息的交换控制编号。

   ![选择筛选器值](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. 现在返回到你正在生成的查询。 查询已通过你选定的筛选器事件和值进行更新。 以前的结果现在也已进行了筛选。

    ![返回到包含筛选结果的查询](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>保存查询供将来使用

1. 从“日志搜索”页的查询中选择“保存”。 为查询命名，选择一个类别，然后选择“保存”。

   ![为查询指定名称和类别](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. 若要查看查询，请选择“收藏夹”。

   ![选择“收藏夹”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. 在“保存的搜索”下，选择查询，以便查看结果。 若要更新查询以便找到不同的结果，请编辑查询。

   ![选择查询](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-the-operations-management-suite-portal"></a>在 Operations Management Suite 门户中查找并运行保存的查询

1. 打开 OMS 工作区中主页 (`https://{your-workspace-name}.portal.mms.microsoft.com`)，然后选择“日志搜索”。

   ![在 OMS 主页上，选择“日志搜索”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -或-

   ![在 OMS 菜单上，选择“日志搜索”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. 在“日志搜索”主页上，选择“收藏夹”。

   ![选择“收藏夹”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. 在“保存的搜索”下，选择查询，以便查看结果。 若要更新查询以便找到不同的结果，请编辑查询。

   ![选择查询](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>后续步骤

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)