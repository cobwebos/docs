---
title: 使用 Azure Monitor 日志监视逻辑应用
description: 通过设置 Azure Monitor 日志和收集 Azure 逻辑应用的诊断数据来排查逻辑应用问题
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270233"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>设置 Azure Monitor 日志并收集 Azure 逻辑应用的诊断数据

若要在运行时获取有关逻辑应用的更丰富的调试信息，你可以设置并使用[Azure Monitor 日志](../azure-monitor/platform/data-platform-logs.md)来记录和存储有关运行时数据和事件的信息，例如触发事件、运行事件和[Log Analytics 工作区](../azure-monitor/platform/resource-logs-collect-workspace.md)中的操作事件。 [Azure Monitor](../azure-monitor/overview.md)可帮助你监视云和本地环境，使你能够更轻松地保持其可用性和性能。 通过使用 Azure Monitor 日志，您可以创建帮助您收集和查看此信息的[日志查询](../azure-monitor/log-query/log-query-overview.md)。 你还可以[将此诊断数据用于其他 azure 服务](#extend-data)，例如 azure 存储和 Azure 事件中心。

若要为逻辑应用设置日志记录，可以在[创建逻辑应用时启用 Log Analytics](#logging-for-new-logic-apps)，也可以在现有逻辑应用的 Log Analytics 工作区中[安装逻辑应用管理解决方案](#install-management-solution)。 此解决方案提供了逻辑应用运行的聚合信息，并包括详细信息，如状态、执行时间、重新提交状态和关联 Id。 然后，若要为此信息启用日志记录和创建查询，请[设置 Azure Monitor 日志](#set-up-resource-logs)。

本文介绍如何在创建逻辑应用时启用 Log Analytics，如何安装和设置逻辑应用管理解决方案，以及如何为 Azure Monitor 日志设置和创建查询。

## <a name="prerequisites"></a>必备条件

在开始之前，需要[Log Analytics 工作区](../azure-monitor/platform/resource-logs-collect-workspace.md)。 如果没有工作区，请了解[如何创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>为新的逻辑应用启用 Log Analytics

创建逻辑应用时，可以打开 Log Analytics。

1. 在[Azure 门户](https://portal.azure.com)的窗格中，在其中提供用于创建逻辑应用的信息，请执行以下步骤：

   1. 在 " **Log Analytics**" 下，选择 **"打开**"。

   1. 从 " **Log Analytics 工作区**" 列表中，选择要将数据从逻辑应用运行发送到的工作区。

      ![提供逻辑应用信息](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      完成此步骤后，Azure 将创建逻辑应用，该应用现在与你的 Log Analytics 工作区相关联。 此外，此步骤还会在工作区中自动安装逻辑应用管理解决方案。

1. 完成操作后，选择“创建”。

1. 运行逻辑应用后，若要查看逻辑应用运行，请[继续执行以下步骤](#view-logic-app-runs)。

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>安装逻辑应用管理解决方案

如果在创建逻辑应用时启用 Log Analytics，请跳过此步骤。 已在 Log Analytics 工作区中安装逻辑应用管理解决方案。

1. 在[Azure 门户](https://portal.azure.com)的 "搜索" 框中，输入 `log analytics workspaces`，然后选择 " **Log Analytics 工作区**"。

   ![选择 "Log Analytics 工作区"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. 在**Log Analytics 工作区**"下，选择工作区。

   ![选择你的 Log Analytics 工作区](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. 在 "**概述**" 窗格的 " **Log Analytics 开始**" > **配置监视解决方案**"下，选择"**查看解决方案**"。

   ![在 "概述" 窗格上，选择 "查看解决方案"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. 在 "**概述**" 下，选择 "**添加**"。

   ![在 "概述" 窗格中添加新的解决方案](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. 打开**Marketplace**后，在 "搜索" 框中输入 "`logic apps management`"，并选择 "**逻辑应用管理**"。

   ![从 Marketplace 选择 "逻辑应用管理"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. 在解决方案说明窗格上，选择 "**创建**"。

   ![选择 "创建" 以添加 "逻辑应用管理" 解决方案](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. 查看并确认要安装解决方案的 Log Analytics 工作区，并再次选择 "**创建**"。

   ![对于 "逻辑应用管理"，选择 "创建"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Azure 将解决方案部署到包含 Log Analytics 工作区的 Azure 资源组后，解决方案将显示在工作区的 "摘要" 窗格中。

   ![工作区摘要窗格](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>设置 Azure Monitor 日志

当你将有关运行时事件和数据的信息存储在[Azure Monitor 日志](../azure-monitor/platform/data-platform-logs.md)中时，你可以创建可帮助你查找和查看此信息的[日志查询](../azure-monitor/log-query/log-query-overview.md)。

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“逻辑应用”。

1. 在逻辑应用菜单的 "**监视**" 下，选择 "**诊断设置**" > "**添加诊断设置**"。

   ![在 "监视" 下，选择 "诊断设置" > "添加诊断设置"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. 若要创建设置，请执行以下步骤：

   1. 提供设置的名称。

   1. 选择“发送到 Log Analytics”。

   1. 对于 "**订阅**"，请选择与 Log Analytics 工作区关联的 Azure 订阅。

   1. 对于**Log Analytics 工作区**，请选择要使用的工作区。

   1. 在 "**日志**" 下，选择 " **WorkflowRuntime** " 类别，其中指定要记录的事件类别。

   1. 若要选择所有指标，请在 "**指标**" 下选择 " **AllMetrics**"。

   1. 完成后，选择“保存”。

   例如：

   ![选择用于日志记录的 Log Analytics 工作区和数据](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>查看逻辑应用运行状态

逻辑应用运行后，可以在 Log Analytics 工作区中查看有关这些运行的数据。

1. 在[Azure 门户](https://portal.azure.com)中，找到并打开 Log Analytics 工作区。

1. 在工作区菜单中，选择 "**工作区摘要** > **逻辑应用管理**"。

   ![逻辑应用运行状态和次数](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > 如果 "逻辑应用管理" 磁贴不会在运行后立即显示结果，请尝试选择 "**刷新**" 或等待一小段时间，然后重试。

   此时，逻辑应用运行将按名称或执行状态进行分组。 此页面还显示有关逻辑应用运行的操作或触发器中的失败的详细信息。

   ![逻辑应用运行的状态摘要](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. 若要查看特定逻辑应用或状态的所有运行，请选择该逻辑应用或状态的行。

   下面是一个显示特定逻辑应用所有运行的示例：

   ![查看逻辑应用运行和状态](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   对于[设置跟踪的属性](#extend-data)的操作，还可以通过在 "**跟踪属性**" 列中选择 "**视图**" 来查看这些属性。 若要搜索跟踪的属性，请使用列筛选器。

   ![查看逻辑应用的跟踪属性](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > 跟踪的属性或已完成事件可能会在 Log Analytics 工作区中出现10-15 分钟的延迟。
   > 此外，此页上的**重新提交**功能当前不可用。

1. 要筛选结果，可以同时执行客户端和服务器端筛选。

   * **客户端筛选器**：对于每个列，请选择所需的筛选器，例如：

     ![示例列筛选器](./media/monitor-logic-apps-log-analytics/filters.png)

   * **服务器端筛选器**：若要选择特定的时间窗口或限制显示的运行次数，请使用页面顶部的范围控件。 默认情况下，一次仅显示 1,000 条记录。

     ![更改时间窗口](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. 若要查看特定运行的所有操作及其详细信息，请选择逻辑应用运行所在的行。

   以下示例显示了特定逻辑应用运行的所有操作和触发器：

   ![查看逻辑应用运行的操作](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>将诊断数据发送到 Azure 存储和 Azure 事件中心

除了 Azure Monitor 日志，你还可以扩展将逻辑应用的诊断数据与其他 Azure 服务配合使用的方式，例如：

* [将 Azure 资源日志存档到存储帐户](../azure-monitor/platform/resource-logs-collect-storage.md)
* [将 Azure 平台日志流式传输到 Azure 事件中心](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

然后，可通过使用其他服务（如 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)和 [Power BI](../azure-monitor/platform/powerbi.md)）的遥测数据和分析来进行实时监视。 例如：

* [将数据从事件中心流式传输到流分析](../stream-analytics/stream-analytics-define-inputs.md)
* [在 Power BI 中使用流分析来分析流数据，并创建实时分析仪表板](../stream-analytics/stream-analytics-power-bi-dashboard.md)

根据要发送诊断数据的位置，请确保先[创建 azure 存储帐户](../storage/common/storage-create-storage-account.md)或[创建 azure 事件中心](../event-hubs/event-hubs-create.md)。 然后，可以选择要将该数据发送到的目标。 仅当使用存储帐户时，保持期才适用。

![将数据发送到 Azure 存储帐户或事件中心](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Azure Monitor 诊断事件

每个诊断事件都有关于逻辑应用和该事件的详细信息，例如状态、开始时间、结束时间等。 若要以编程方式设置监视、跟踪和日志记录，可以将此信息与[适用于 Azure 逻辑应用的 REST API](https://docs.microsoft.com/rest/api/logic)和[Azure Monitor 的 REST API](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows)一起使用。 你还可以使用 "`clientTrackingId`" 和 "`trackedProperties`" 属性 "，这些属性显示在 

* `clientTrackingId`：如果未提供，Azure 会自动生成此 ID，并跨逻辑应用运行关联事件，包括从逻辑应用调用的任何嵌套工作流。 可以通过在触发器请求中传递包含自定义 ID 值的 `x-ms-client-tracking-id` 标头来在触发器中手动指定此 ID。 可以使用请求触发器、HTTP 触发器或 webhook 触发器。

* `trackedProperties`：若要在诊断数据中跟踪输入或输出，可以通过使用逻辑应用设计器或直接在逻辑应用的 JSON 定义中，将 `trackedProperties` 部分添加到操作。 跟踪属性只能跟踪单个操作输入和输出，不过可以使用事件的 `correlation` 属性以便跨运行中的操作进行关联。 若要跟踪多个属性（一个或多个属性），请将所需的 `trackedProperties` 部分和属性添加到操作定义。

  下面的示例演示了**初始化变量**操作定义如何包含操作输入中的跟踪属性，其中输入是数组，而不是记录。

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  此示例显示多个跟踪的属性：

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

此示例演示 `ActionCompleted` 事件如何包括 `clientTrackingId` 和 `trackedProperties` 特性：

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>后续步骤

* [创建监视和跟踪查询](../logic-apps/create-monitoring-tracking-queries.md)
* [利用 Azure Monitor 日志监视 B2B 消息](../logic-apps/monitor-b2b-messages-log-analytics.md)
