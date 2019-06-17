---
title: 监视逻辑应用与 Azure Monitor 日志-Azure 逻辑应用 |Microsoft Docs
description: 使用 Azure Log Analytics 获取见解和调试数据，以对逻辑应用运行进行故障排除和诊断
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 3f890e6cabd757fdd38374befaaccd1a10c9bd96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106204"
---
# <a name="monitor-logic-apps-with-azure-monitor-logs"></a>使用 Azure Monitor 日志监视逻辑应用

若要监视并获取有关逻辑应用更丰富的调试详细信息，开启[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)创建逻辑应用时。 Azure Monitor 日志提供了诊断日志记录和监视逻辑应用的 Azure 门户中安装逻辑应用管理解决方案时。 此解决方案还为逻辑应用运行提供聚合信息，其中包含状态、执行时间、重新提交状态和相关 ID 等特定的详细信息。 本文介绍如何启用 Azure Monitor 日志，以便可以查看运行时事件和运行逻辑应用的数据。

若要打开现有逻辑应用的 Azure Monitor 日志，请按照这些步骤[启用诊断日志记录并将逻辑应用运行时数据发送到 Azure Monitor 日志](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

> [!NOTE]
> 此页面之前描述了如何使用 Microsoft Operations Management Suite (OMS) 执行这些任务的步骤，该解决方案将[在 2019 年 1 月停用](../azure-monitor/platform/oms-portal-transition.md)，取而代之的将是使用 Azure Log Analytics 执行这些步骤的内容。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>必备组件

在开始之前，需要一个 Log Analytics 工作区。 了解[如何创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>创建逻辑应用时请启用诊断日志记录

1. 在 [Azure 门户](https://portal.azure.com)中，创建一个逻辑应用。 选择“创建资源” > “集成” > “逻辑应用”。   

   ![创建逻辑应用](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. 在“创建逻辑应用”  下，如下所述执行这些任务：

   1. 为逻辑应用命名，并选择 Azure 订阅。 

   1. 创建或选择 Azure 资源组。

   1. 将“Log Analytics”  设置为“开”  。 

   1. 从 Log Analytics 工作区列表中，选择要从中发送逻辑应用运行数据的 Log Analytics 工作区。 

      ![创建逻辑应用](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      完成此步骤后，Azure 将创建逻辑应用，该应用现在与你的 Log Analytics 工作区相关联。 
      此外，此步骤还会自动在你的工作区中安装逻辑应用管理解决方案。

   1. 完成后，选择“创建”  。

1. 若要查看逻辑应用运行，请[继续执行这些步骤](#view-logic-app-runs-oms)。

## <a name="install-logic-apps-management-solution"></a>安装逻辑应用管理解决方案

如果已启用了 Azure Monitor 日志创建逻辑应用时，跳过此步骤。 你已经安装了逻辑应用管理解决方案。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“所有服务”。  在搜索框中查找“log analytics”，并选择“Log Analytics”  。

   ![选择“Log Analytics”](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. 在“Log Analytics”下  ，查找并选择你的 Log Analytics 工作区。 

   ![选择你的 Log Analytics 工作区](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. 在“开始使用 Log Analytics” > “配置监视解决方案”下，选择“查看解决方案”    。

   ![选择“查看解决方案”](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. 在“概述”页上，选择“添加”  ，这将打开“管理解决方案”列表。  从该列表中选择“逻辑应用管理”  。 

   ![选择“逻辑应用管理”](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   如果未找到此解决方案，请在列表底部选择“加载更多”，直到此解决方案出现。 

1. 选择“创建”，确认要在其中安装解决方案的 Log Analytics 工作区，然后再次选择“创建”   。   

   ![为“逻辑应用管理”选择“创建”](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

   如果不想使用现有工作区，还可以在这次创建新工作区。

   完成后，逻辑应用管理解决方案将显示在“概述”页面上。 

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>查看逻辑应用运行信息

逻辑应用运行后，可在“逻辑应用管理”磁贴查看状态及逻辑应用运行次数  。 

1. 转到 Log Analytics 工作区，然后打开“概览”页。 选择“逻辑应用管理”  。 

   ![逻辑应用运行状态和次数](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   此时，逻辑应用运行将按名称或执行状态进行分组。 
   此页面还显示有关逻辑应用运行的操作或触发器中的失败的详细信息。

   ![逻辑应用运行的状态摘要](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. 要查看特定逻辑应用或状态的所有运行，请选择逻辑应用或状态的行。

   下面是一个显示特定逻辑应用所有运行的示例：

   ![查看逻辑应用或状态的运行](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   此页面具有以下高级选项：

   * **跟踪的属性：**

     此列显示逻辑应用的跟踪属性（按操作分组）。 若要查看跟踪属性，请选择“查看”  。 
     若要搜索跟踪的属性，请使用列筛选器。
   
     ![查看逻辑应用的跟踪属性](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     任何新添加的跟踪属性可能需要 10-15 分钟时间，才会首次显示。 了解[如何将跟踪属性添加到逻辑应用](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)。

   * **重新提交：** 可以重新提交一个或多个已失败、成功或仍在运行的逻辑应用运行。 选择要重新提交的运行所对应的复选框，然后选择“重新提交”  。 

     ![重新提交逻辑应用运行](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. 要筛选这些结果，可以执行客户端和服务器端筛选。

   * **客户端筛选器**：对于每个列，请选择所需的筛选器，例如：

     ![示例列筛选器](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **服务器端筛选器**：要选择特定的时间窗口或限制显示的运行次数，请使用页面顶部的作用域控件。 默认情况下，一次仅显示 1,000 条记录。
   
     ![更改时间窗口](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. 若要查看特定运行的所有操作及其详细信息，请选择逻辑应用运行所在的行。

   下面是一个显示特定逻辑应用运行的所有操作的示例：

   ![查看逻辑应用运行的操作](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. 在任何结果页上，若要查看结果背后的查询或查看所有结果，请选择“查看全部”  ，这将打开“日志搜索”页。
   
   ![结果页上的“查看全部”](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   在“日志搜索”页上，

   * 若要查看表中的查询结果，请选择“表”  。

   * 要更改查询，可在搜索栏中编辑查询字符串。 
   要获取更好的体验，请选择“高级分析”  。

     ![查看逻辑应用运行的操作和详细信息](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     在 log analytics 页上，您可以更新查询并查看表的结果。 此查询使用 [Kusto 查询语言](https://aka.ms/LogAnalyticsLanguageReference)，如果希望查看不同的结果，可对其进行编辑。 

     ![log analytics-查询视图](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>后续步骤

* [监视 B2B 消息](../logic-apps/logic-apps-monitor-b2b-message.md)