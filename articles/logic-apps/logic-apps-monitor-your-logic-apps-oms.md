---
title: 监视逻辑应用与 Azure Monitor
description: 获取用于排查和诊断逻辑应用运行 Azure Monitor 日志的见解和调试数据
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/29/2019
ms.openlocfilehash: 305b50c86a468354f049fcc57fcb79b537e8dfed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791892"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>使用 Azure Monitor 日志获取逻辑应用的见解和调试数据

若要监视并获取有关逻辑应用的更丰富的调试详细信息，请在创建逻辑应用时打开[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)。 当你在 Azure 门户中安装逻辑应用管理解决方案时，Azure Monitor 日志为逻辑应用提供诊断日志记录和监视功能。 此解决方案还为逻辑应用运行提供聚合信息，其中包含状态、执行时间、重新提交状态和相关 ID 等特定的详细信息。 本文介绍如何打开 Azure Monitor 日志，以便查看逻辑应用运行的运行时事件和数据。

本主题说明如何在创建逻辑应用时设置 Azure Monitor 日志。 若要打开现有逻辑应用 Azure Monitor 日志，请按照以下步骤[启用诊断日志记录并将逻辑应用运行时数据发送到 Azure Monitor 日志](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

> [!NOTE]
> 此页前面介绍了如何通过[2019 年1月停](../azure-monitor/platform/oms-portal-transition.md)用的 MICROSOFT OPERATIONS MANAGEMENT SUITE （OMS）执行这些任务，并将这些步骤替换为[Azure Monitor 日志](../azure-monitor/platform/data-platform-logs.md)，这些步骤替换了 Log Analytics 的术语。 日志数据仍然存储在 Log Analytics 工作区中，并仍然由同一 Log Analytics 服务收集并分析。 有关详细信息，请参阅[Azure Monitor 术语更改](../azure-monitor/terminology.md)。

## <a name="prerequisites"></a>必备组件

在开始之前，需要一个 Log Analytics 工作区。 了解[如何创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。

## <a name="turn-on-logging-for-new-logic-apps"></a>为新的逻辑应用启用日志记录

1. 在[Azure 门户](https://portal.azure.com)中创建逻辑应用。 在 Azure 主菜单中，依次选择“创建资源” > “集成” > “逻辑应用”。

   ![创建新的逻辑应用](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. 在 "**逻辑应用**" 下，执行以下步骤：

   1. 为逻辑应用命名，并选择 Azure 订阅。

   1. 创建或选择 Azure 资源组。 选择逻辑应用的位置。

   1. 在 " **Log Analytics**" 下，选择 **"打开**"。

   1. 从 " **Log Analytics 工作区**" 列表中，选择要将数据从逻辑应用运行发送到的工作区。

      ![提供逻辑应用信息](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      完成此步骤后，Azure 将创建逻辑应用，该应用现在与你的 Log Analytics 工作区相关联。 此外，此步骤还会在工作区中自动安装逻辑应用管理解决方案。

   1. 完成操作后，选择“创建”。

1. 若要查看逻辑应用运行，请[继续执行这些步骤](#view-logic-app-runs-oms)。

## <a name="install-logic-apps-management-solution"></a>安装逻辑应用管理解决方案

如果在创建逻辑应用时已设置 Azure Monitor 日志，请跳过此步骤。 你已经安装了逻辑应用管理解决方案。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“所有服务”。 在搜索框中，找到 "log analytics 工作区"，然后选择 " **Log Analytics 工作区**"。

   ![选择 "Log Analytics 工作区"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. 在**Log Analytics 工作区**"下，选择工作区。

   ![选择你的 Log Analytics 工作区](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. 在 "概述" 窗格的 " **Log Analytics 开始**" > **配置监视解决方案**"下，选择"**查看解决方案**"。

   ![选择 "查看解决方案"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. 在 "**概述**" 下，选择 "**添加**"。

   ![选择“添加”](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. 打开**Marketplace**后，在 "搜索" 框中输入 "逻辑应用管理"，并选择 "**逻辑应用**管理"。

   ![选择“逻辑应用管理”](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. 在解决方案说明窗格上，选择 "**创建**"。

   ![对于 "逻辑应用管理"，选择 "创建"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. 查看并确认要安装解决方案的 Log Analytics 工作区，并再次选择 "**创建**"。

   ![对于 "逻辑应用管理"，选择 "创建"](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   Azure 将解决方案部署到包含 Log Analytics 工作区的 Azure 资源组后，解决方案将显示在工作区的 "摘要" 窗格中。

   ![工作区摘要窗格](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>查看逻辑应用运行信息

逻辑应用运行后，可在“逻辑应用管理”磁贴查看状态及逻辑应用运行次数。

1. 中转到 Log Analytics 工作区，并选择 "**工作区摘要**" > **逻辑应用管理**"。

   ![逻辑应用运行状态和次数](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   此时，逻辑应用运行将按名称或执行状态进行分组。 此页面还显示有关逻辑应用运行的操作或触发器中的失败的详细信息。

   ![逻辑应用运行的状态摘要](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. 要查看特定逻辑应用或状态的所有运行，请选择逻辑应用或状态的行。

   下面是一个显示特定逻辑应用所有运行的示例：

   ![查看逻辑应用运行和状态](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   此页面包含高级选项： 

   * **跟踪属性**列：对于设置跟踪属性（按操作分组）的逻辑应用，可以从此列查看这些属性。 若要查看这些跟踪的属性，请选择 "**查看**"。 若要搜索跟踪的属性，请使用列筛选器。

      ![查看逻辑应用的跟踪属性](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

      任何新添加的跟踪属性可能需要 10-15 分钟时间，才会首次显示。 了解[如何将跟踪属性添加到逻辑应用](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)。

   * **重新提交**：你可以重新提交一个或多个已失败、成功或仍在运行的逻辑应用运行。 选中要重新提交的运行对应的复选框，然后选择 "**重新提交**"。

     ![重新提交逻辑应用运行](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. 要筛选结果，可以同时执行客户端和服务器端筛选。

   * **客户端筛选器**：对于每个列，请选择所需的筛选器，例如：

     ![示例列筛选器](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **服务器端筛选器**：若要选择特定的时间窗口或限制显示的运行次数，请使用页面顶部的范围控件。 默认情况下，一次仅显示 1,000 条记录。

     ![更改时间窗口](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. 若要查看特定运行的所有操作及其详细信息，请选择逻辑应用运行所在的行。

   以下示例显示了特定逻辑应用运行的所有操作和触发器：

   ![查看逻辑应用运行的操作](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. 在任何结果页上，若要查看结果后的查询或查看所有结果，请选择 "**查看全部**"，这将打开 "**日志**" 页。

   ![查看所有结果](media/logic-apps-monitor-your-logic-apps-oms/logic-app-see-all.png)

   在 "**日志**" 页上，可以选择以下选项：

   * 若要在表中查看查询结果，请选择 "**表**"。

   * 查询使用[Kusto 查询语言](https://aka.ms/LogAnalyticsLanguageReference)，如果想要查看不同的结果，可以编辑该语言。 若要更改查询，请更新查询字符串，然后选择 "**运行**" 以查看表中的结果。 

     ![Log Analytics-查询视图](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>后续步骤

* [监视 B2B 消息](../logic-apps/logic-apps-monitor-b2b-message.md)