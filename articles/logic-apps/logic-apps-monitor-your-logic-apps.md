---
title: "检查状态、设置日志记录并获取警报 - Azure 逻辑应用 | Microsoft Docs"
description: "监视逻辑应用的状态和性能，记录诊断数据并设置警报"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: f0376070a8bec7d97f54e9a198d5d67a9751334c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>针对 Azure 逻辑应用监视状态、设置诊断日志记录，并启用警报

在[创建并运行逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)后，可以检查其运行历史记录、触发历史记录、状态和性能。 对于实时事件监视和更丰富的调试，为逻辑应用设置[诊断记录](#azure-diagnostics)。 这样，就可以[查找和查看事件](#find-events)，例如触发事件、运行事件和操作事件。 还可以将此[诊断数据用于其他服务](#extend-diagnostic-data)，如 Azure 存储和 Azure 事件中心。 

要获取关于故障或其他可能问题的通知，请设置[警报](#add-azure-alerts)。 例如，可以创建一个警报，用于检测“一小时内运行失败超过五次的情况”。 还可以使用 [Azure 诊断事件设置和属性](#diagnostic-event-properties)以编程方式设置监视、跟踪和记录。

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>查看逻辑应用的运行和触发历史记录

1. 若要在 [Azure 门户](https://portal.azure.com)中查找逻辑应用，请在 Azure 主菜单上选择“所有服务”。 在搜索框中键入“逻辑应用”，然后选择“逻辑应用”。

   ![查找逻辑应用](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Azure 门户显示与 Azure 订阅关联的所有逻辑应用。 

2. 选择逻辑应用，然后选择“概述”。

   Azure 门户显示逻辑应用的运行历史记录和触发历史记录。 例如：

   ![逻辑应用运行历史记录和触发历史记录](media/logic-apps-monitor-your-logic-apps/overview.png)

   * “运行历史记录”显示逻辑应用的所有运行信息。 
   * “触发历史记录”显示逻辑应用的所有触发活动。

   有关状态描述，请参阅[排查逻辑应用故障](../logic-apps/logic-apps-diagnosing-failures.md)。

   > [!TIP]
   > 如果找不到预期的数据，请在工具栏中选择“刷新”。

3. 要查看特定运行的步骤，请在“运行历史记录”下选择该运行。 

   监视器视图显示该运行中的每个步骤。 例如：

   ![针对特定运行的操作](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. 若要获取有关运行的详细信息，请选择“运行详细信息”。 此信息总结了运行的步骤、状态、输入和输出。 

   ![选择“运行详细信息”](media/logic-apps-monitor-your-logic-apps/run-details.png)

   例如，可获得运行的“相关 ID”，在使用[适用于逻辑应用的 REST API](https://docs.microsoft.com/rest/api/logic) 时，可能需要该信息。

5. 若要获取有关特定步骤的详细信息，请选择该步骤。 现在可以查看详细信息，如输入、输出以及该步骤中出现的任何错误。 例如：

   ![步骤详细信息](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > 所有运行时详细信息和事件都在逻辑应用服务中进行加密。 只有当用户请求查看该数据时，才会进行解密。 还可以通过 [Azure 基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-what-is.md) 来控制对这些事件的访问。

6. 要获取有关特定触发事件的详细信息，请返回到“概述”窗格。 在“触发历史记录”下选择“触发事件”。 现在可以查看输入和输出等详细信息，例如：

   ![触发事件输出详细信息](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>为逻辑应用启用诊断日志记录

对于针对运行时详细信息和事件的更丰富调试，可以使用 [Azure Log Analytics](../log-analytics/log-analytics-overview.md) 设置诊断日志记录。 Log Analytics 是 [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中的一项服务，用于监视云和本地环境，有助于保持其较高的可用性和性能。 

在开始之前，需要具有 OMS 工作区。 了解[如何创建 OMS 工作区](../log-analytics/log-analytics-get-started.md).

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“逻辑应用”。 

2. 在“逻辑应用”边栏选项卡菜单的“监视”下，选择“诊断” > “诊断设置”。

   ![依次转到“监视”、“诊断”、“诊断设置”](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. 在“诊断设置”下，选择“打开”。

   ![启用诊断日志](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. 现在选择日志记录的 OMS 工作区和事件类别，如下所示：

   1. 选择“发送到 Log Analytics”。 
   2. 在“Log Analytics”下，选择“配置”。 
   3. 在“OMS 工作区”下，选择要用于日志记录的 OMS 工作区。
   4. 在“日志”下，选择“WorkflowRuntime”类别。
   5. 选择指标间隔。
   6. 完成后，选择“保存”。

   ![选择 OMS 工作区和数据进行日志记录](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

现在可以为触发事件、运行事件和操作事件查找事件和其他数据。

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>查找逻辑应用的事件和数据

要在逻辑应用中查找和查看事件（如触发事件、运行事件和操作事件），请按照下列步骤操作。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“所有服务”。 搜索“log analytics”，然后选择“Log Analytics”，如下所示：

   ![选择“Log Analytics”](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. 在“Log Analytics”下，查找并选择“OMS 工作区”。 

   ![选择你的 OMS 工作区](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. 在“管理”下，选择“OMS 门户”。

   ![选择“OMS 门户”](media/logic-apps-monitor-your-logic-apps/omsportalpage.png)

4. 在 OMS 主页上，选择“日志搜索”。

   ![在 OMS 主页上，选择“日志搜索”](media/logic-apps-monitor-your-logic-apps/logsearch.png)

   -或-

   ![在 OMS 菜单上，选择“日志搜索”](media/logic-apps-monitor-your-logic-apps/logsearch-2.png)

5. 在搜索框中，指定要查找的字段，然后按下 Enter 键。 开始输入时，OMS 会显示你可以使用的可能的匹配和操作。 

   例如，要查找发生的前 10 个事件，请输入并选择以下搜索查询：Category=WorkflowRuntime |top 10

   ![输入搜索字符串](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   详细了解[如何在 Log Analytics 中查找数据](../log-analytics/log-analytics-log-searches.md)。

6. 在结果页的左侧栏中，选择要查看的时间范围。
若要通过添加筛选器优化查询，请选择“+添加”。

   ![选择查询结果的时间范围](media/logic-apps-monitor-your-logic-apps/query-results.png)

7. 在“添加筛选器”下，输入筛选器名称，以便查找所需的筛选器。 选择筛选器，然后选择“+添加”。

   本示例使用“status”一词在“AzureDiagnostics”下查找失败的事件。
   此处已选中 status_s 的筛选器。

   ![选择筛选器](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

8. 在左侧栏中，选择要使用的筛选器值，然后选择“应用”。

   ![选择筛选器值，然后选择“应用”](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

9. 现在返回到你正在生成的查询。 该查询已通过你选定的筛选器和值进行更新。 以前的结果现在也已进行了筛选。

   ![返回到包含筛选结果的查询](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

10. 若要保存查询供将来使用，请选择“保存”。 了解[如何保存查询](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query)。

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>扩展将诊断数据用于其他服务的方式和位置

结合使用 Azure Log Analytics 后，可以扩展将逻辑应用的诊断数据用于其他 Azure 服务的方式，例如： 

* [在 Azure 存储中存档 Azure 诊断日志](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [将 Azure 诊断日志流式传输到 Azure 事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

然后，可通过使用其他服务（如 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)和 [Power BI](../log-analytics/log-analytics-powerbi.md)）的遥测数据和分析来进行实时监视。 例如：

* [将数据从事件中心流式传输到流分析](../stream-analytics/stream-analytics-define-inputs.md)
* [在 Power BI 中使用流分析来分析流数据，并创建实时分析仪表板](../stream-analytics/stream-analytics-power-bi-dashboard.md)

根据要设置的选项，确保首先[创建 Azure 存储帐户](../storage/common/storage-create-storage-account.md)或[创建 Azure 事件中心](../event-hubs/event-hubs-create.md)。 然后选择要发送诊断数据的位置选项：

![将数据发送到 Azure 存储帐户或事件中心](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> 仅当你选择使用存储帐户时才应用保留期。

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>为逻辑应用设置警报

要监视逻辑应用的特定指标或超出的阈值，请[在 Azure 中设置警报](../monitoring-and-diagnostics/monitoring-overview-alerts.md)。 了解 [Azure 中的指标](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。 

若要在不使用 [Azure Log Analytics](../log-analytics/log-analytics-overview.md) 的情况下设置警报，请按照下列步骤执行。 有关更高级的警报条件和操作，也可以[设置 Log Analytics](#azure-diagnostics)。

1. 在“逻辑应用”边栏选项卡菜单的“监视”下，选择“诊断” > “警报规则” > “添加警报”，如下所示：

   ![为逻辑应用添加警报](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. 在“添加警报规则”边栏选项卡上，创建如下所示的警报：

   1. 在“资源”下，如果未选中“逻辑应用”，则选中它。 
   2. 为警报提供名称和描述。
   3. 选择要跟踪的指标或事件。
   4. 选择“条件”、指定指标的“阈值”，然后选择用于监视此指标的“时间段”。
   5. 选择是否发送警报的邮件。 
   6. 指定用于发送警报的任何其他电子邮件地址。 
   还可以指定发送警报的 webhook URL。

   例如，如果在一小时内有五个或更多个运行失败，则此规则会发送警报：

   ![创建指标警报规则](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> 要从警报运行逻辑应用，可以在工作流中包括[请求触发器](../connectors/connectors-native-reqres.md)，从而可以执行类似以下示例的任务：
> 
> * [发布到 Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [发送短信](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [向队列添加消息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Azure 诊断事件设置和详细信息

每个诊断事件都有关于逻辑应用和该事件的详细信息，例如状态、开始时间、结束时间等。 若要以编程方式设置监视、跟踪和日志记录，可以将这些详细信息用于 [Azure 逻辑应用适用的 REST API](https://docs.microsoft.com/rest/api/logic) 和 [Azure 诊断适用的 REST API](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows)。

例如，`ActionCompleted` 事件具有可用于跟踪和监视的 `clientTrackingId` 和 `trackedProperties` 属性：

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
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
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
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
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`：如果未提供，Azure 会自动生成此 ID，并跨逻辑应用运行关联事件，包括从逻辑应用调用的任何嵌套工作流。 可通过在触发器请求中传递带有自定义 ID 值的 `x-ms-client-tracking-id` 标头，从触发器手动指定此 ID。 可以使用请求触发器、HTTP 触发器或 webhook 触发器。

* `trackedProperties`：要在诊断数据中跟踪输入或输出，可将跟踪属性添加到逻辑应用的 JSON 定义中的操作。 跟踪属性只能跟踪单个操作输入和输出，不过可以使用事件的 `correlation` 属性以便跨运行中的操作进行关联。

  要跟踪一个或多个属性，请添加 `trackedProperties` 部分和所需操作定义的属性。 例如，假设要在遥测中跟踪类似于“订单 ID”的数据：

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```

## <a name="next-steps"></a>后续步骤

* [创建模板以用于逻辑应用部署和版本管理](../logic-apps/logic-apps-create-deploy-template.md)
* [具有 Enterprise Integration Pack 的 B2B 方案](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [监视 B2B 消息](../logic-apps/logic-apps-monitor-b2b-message.md)