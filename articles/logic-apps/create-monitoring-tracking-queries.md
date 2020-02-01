---
title: 查看和创建 Azure Monitor 日志中逻辑应用的查询
description: 在 Azure 逻辑应用 Azure Monitor 日志中查看和创建查询
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908067"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>查看并创建用于在 Azure 逻辑应用 Azure Monitor 日志中监视和跟踪的查询

您可以查看从[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)生成结果的基础查询，并创建根据特定条件筛选结果的查询。 例如，您可以基于特定的交换控制编号查找消息。 查询使用[Kusto 查询语言](https://aka.ms/LogAnalyticsLanguageReference)，如果想要查看不同的结果，可以对其进行编辑。 有关详细信息，请参阅[Azure Monitor 日志查询](../azure-monitor/log-query/query-language.md)。

## <a name="prerequisites"></a>Prerequisites

* Log Analytics 工作区。 如果没有 Log Analytics 工作区，请了解[如何创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。

* 使用 Azure Monitor 日志记录设置的逻辑应用，并将该信息发送到 Log Analytics 工作区。 了解[如何为逻辑应用设置 Azure Monitor 日志](../logic-apps/monitor-logic-apps.md)。

* 如果使用的是集成帐户，请确保已设置帐户 Azure Monitor 日志记录，以便将该信息发送到 Log Analytics 工作区。 了解如何[为集成帐户设置 Azure Monitor 日志记录](../logic-apps/monitor-b2b-messages-log-analytics.md)。

## <a name="view-queries-behind-results"></a>查看结果的隐藏查询

若要查看或编辑在工作区摘要中产生结果的查询，请执行以下步骤：

1. 在任何结果页的底部，选择 "**查看全部**"。

   ![查看所有结果](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   "日志" 页将打开，并显示 "上一个结果" 页后面的查询。

   ![日志页-查询视图](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. 在 "**日志**" 页上，可以选择以下选项：

   * 若要以表的形式查看查询结果，请在查询编辑器下选择 "**表**"。

   * 若要更改查询，请更新查询字符串，然后选择 "**运行**" 以查看表中的结果。

## <a name="create-your-own-query"></a>创建自己的查询

若要基于特定属性或值查找或筛选结果，可以通过从空查询开始或使用现有查询来创建自己的查询。 有关详细信息，请参阅[Azure Monitor 中的日志查询入门](../azure-monitor/log-query/get-started-queries.md)。

1. 在[Azure 门户](https://portal.azure.com)中，查找并选择 Log Analytics 工作区。

1. 在工作区菜单中的 "**常规**" 下，选择 "**日志**"。

1. 从空查询或任何可用的现有查询开始。

   * 若要检查是否存在任何现有查询，请在 "查询" 工具栏上，选择 "**示例查询**" > "**历史记录**"，其中显示了之前的查询运行的查询，或 "**查询资源管理器**" （显示预生成的查询）

     例如，逻辑应用 B2B 解决方案提供以下预生成的查询：

     ![从 "逻辑应用 B2B" 解决方案预生成的查询开始](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * 若要从空查询开始，请在查询编辑器中，开始键入查询的[Kusto 查询语言](../azure-monitor/log-query/query-language.md)。

     ![从空查询开始](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>后续步骤

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)