---
title: 在 Azure 监视器日志中查看和创建逻辑应用的查询
description: 在 Azure 监视日志中查看和创建 Azure 逻辑应用的查询
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908067"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>查看和创建查询，以便监视和跟踪 Azure 逻辑应用的 Azure 监视器日志

您可以查看从[Azure 监视器日志](../log-analytics/log-analytics-overview.md)生成结果的基础查询，并创建基于特定条件筛选结果的查询。 例如，你可以根据特定的交换控制编号查找消息。 查询使用[Kusto 查询语言](https://aka.ms/LogAnalyticsLanguageReference)，如果要查看不同结果，可以对其进行编辑。 有关详细信息，请参阅[Azure 监视器日志查询](../azure-monitor/log-query/query-language.md)。

## <a name="prerequisites"></a>先决条件

* Log Analytics 工作区。 如果没有 Log Analytics 工作区，请了解[如何创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。

* 使用 Azure 监视器日志记录设置并将该信息发送到日志分析工作区的逻辑应用。 [了解如何为逻辑应用设置 Azure 监视器日志](../logic-apps/monitor-logic-apps.md)。

* 如果使用集成帐户，请确保已使用 Azure 监视器日志记录设置帐户，以将该信息发送到日志分析工作区。 了解如何[为集成帐户设置 Azure 监视器日志记录](../logic-apps/monitor-b2b-messages-log-analytics.md)。

## <a name="view-queries-behind-results"></a>查看结果后面的查询

要查看或编辑在工作区摘要中生成结果的查询，请按照以下步骤操作：

1. 在任何结果页上，在底部，选择 **"查看所有**"。

   ![查看所有结果](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   "日志"页将打开并显示上一个结果页后面的查询。

   ![日志页 - 查询视图](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. 在 **"日志"** 页上，您可以选择以下选项：

   * 要将查询结果视为表，请在查询编辑器下选择**表**。

   * 要更改查询，请更新查询字符串，然后选择 **"运行"** 以查看表中的结果。

## <a name="create-your-own-query"></a>创建您自己的查询

要查找或筛选基于特定属性或值的结果，可以通过从空查询开始或使用现有查询来创建自己的查询。 有关详细信息，请参阅 Azure[监视器 中的日志查询入门](../azure-monitor/log-query/get-started-queries.md)。

1. 在[Azure 门户](https://portal.azure.com)中，查找并选择日志分析工作区。

1. 在工作区菜单中，在 **"常规"** 下，选择 **"日志**"。

1. 从空查询或任何可用的现有查询开始。

   * 要检查是否存在任何现有查询可用，请在查询工具栏上选择 **"示例查询** > **历史记录**"（显示以前查询运行中的查询）或选择显示预构建查询的**查询资源管理器**。

     例如，逻辑应用 B2B 解决方案提供以下预构建查询：

     ![从"逻辑应用 B2B"解决方案开始预构建查询](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * 要从空查询开始，在查询编辑器中，开始键入查询的[Kusto 查询语言](../azure-monitor/log-query/query-language.md)。

     ![从空查询开始](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>后续步骤

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)