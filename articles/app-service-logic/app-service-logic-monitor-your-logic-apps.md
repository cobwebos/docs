---
title: "在 Azure 应用服务中监视逻辑应用 | Microsoft Docs"
description: "如何查看逻辑应用进行的操作"
author: jeffhollan
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cc73caf6ffdc47199369112a5310a3f4dd0cc9bf


---
# <a name="monitor-your-logic-apps"></a>监视逻辑应用
[创建逻辑应用](app-service-logic-create-a-logic-app.md)之后，可以在 Azure 门户中查看其执行的完整历史记录。  还可以设置服务（如 Azure 诊断和 Azure 警报）以实时监视事件，以及针对“一小时内运行失败次数超过 5 次”的事件发出警报。

## <a name="monitor-in-the-azure-portal"></a>在 Azure 门户中进行监视
若要查看历史记录，请选择“浏览”，然后选择“逻辑应用”。 会显示订阅中所有逻辑应用的列表。  选择要监视的逻辑应用。  你会看到对此逻辑应用进行的所有操作和触发的列表。

![概述](./media/app-service-logic-monitor-your-logic-apps/overview.png)

此边栏选项卡上有一些很有帮助的部分：

* “摘要”会列出“所有运行”和“触发历史记录”
  * “所有运行”会列出最新逻辑应用运行。  可以单击任何行以了解有关运行的详细信息行，或单击磁贴以列出更多运行。
  * “触发历史记录”会列出此逻辑应用的所有触发活动。  触发活动可以针对新数据的“已跳过”检查（例如希望查看新文件是否已添加到 FTP）、表示返回了数据以触发逻辑应用的“成功”或是与配置中的错误对应的“失败”。
* “诊断”使你可以查看运行时详细信息和事件，并订阅 [Azure 警报](#adding-azure-alerts)

> [!NOTE]
> 所有运行时详细信息和事件都在逻辑应用服务中进行静态加密。 它们只能在用户发出视图请求时进行解密。 还可以通过 Azure 基于角色的访问控制 (RBAC) 来控制对这些事件的访问。
> 
> 

### <a name="view-the-run-details"></a>查看运行详细信息
此运行列表显示特定运行的“状态”、“开始时间”和“持续时间”。 选择任何行可查看有关该运行的详细信息。

监视视图显示运行的每个步骤、输入和输出以及可能出现的任何错误消息。

![运行和操作](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

如果需要任何其他详细信息（如运行的“相关 ID”（可以用于 REST API）），则可以单击“运行详细信息”按钮。  这包括运行的所有步骤、状态和输入/输出。

## <a name="azure-diagnostics-and-alerts"></a>Azure 诊断和警报
除了以上由 Azure 门户和 REST API 提供的详细信息，还可以配置逻辑应用以使用 Azure 诊断来获取更丰富的详细信息和进行调试。

1. 单击逻辑应用边栏选项卡的“诊断”部分
2. 单击可配置“诊断设置”
3. 配置将数据发出到的事件中心或存储帐户
   
    ![Azure 诊断设置](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### <a name="adding-azure-alerts"></a>添加 Azure 警报
配置诊断之后，可以添加 Azure 警报以在超过特定阈值时触发。  在“诊断”边栏选项卡中，选择“警报”磁贴和“添加警报”。  这会引导你完成基于一些阈值和指标来配置警报的过程。

![Azure 警报指标](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

可以根据需要配置“条件”、“阈值”和“期间”。  最后，可以配置将通知发送到的电子邮件地址，或配置 webhook。  还可以在逻辑应用中使用[请求触发器](../connectors/connectors-native-reqres.md)以对警报运行（用于执行诸如[发布到 Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)、[发送文本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)或[向队列添加消息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)这类操作）。

### <a name="azure-diagnostics-settings"></a>Azure 诊断设置
每个事件都包含有关逻辑应用和事件可能状态的详细信息。  下面是 ActionCompleted 事件的示例：

```javascript
{
            "time": "2016-07-09T17:09:54.4773148Z",
            "workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
            "resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
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
                    "subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
                    "resourceGroupName": "MyResourceGroup",
                    "workflowId": "cff00d5458f944d5a766f2f9ad142553",
                    "workflowName": "MyLogicApp",
                    "runId": "08587361146922712057",
                    "location": "eastus",
                    "actionName": "Http"
                },
                "correlation": {
                    "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
                    "clientTrackingId": "my-custom-tracking-id"
                },
                "trackedProperties": {
                    "myProperty": "<value>"
                }
            }
        }
```

对于跟踪和监视特别有用的两个属性是 clientTrackingId 和 trackedProperties。  

#### <a name="client-tracking-id"></a>客户端跟踪 ID
客户端跟踪 ID 是一个值，使各个事件跨逻辑应用运行相关联，包括作为逻辑应用一部分调用的任何嵌套工作流。  此 ID 在未提供时会自动生成，不过可以通过在触发器请求（请求触发器、HTTP 触发器或 webhook 触发器）中传递具有 ID 值的 `x-ms-client-tracking-id` 标头，从触发器手动指定客户端跟踪 ID。

#### <a name="tracked-properties"></a>跟踪属性
跟踪属性可以在工作流定义中添加到操作，以在诊断数据中跟踪输入或输出。  如果要在遥测中跟踪类似于“订单 ID”的数据，这可能会十分有用。  若要添加跟踪属性，请在操作中包含 `trackedProperties` 属性。  跟踪属性只能跟踪单个操作输入和输出，不过可以使用事件的 `correlation` 属性以便跨运行中的操作进行关联。

```javascript
{
    "myAction": {
        "type": "http",
        "inputs": {
            "uri": "http://uri",
            "headers": {
                "Content-Type": "application/json"
            },
            "body": "@triggerBody()"
        },
        "trackedProperties":{
            "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
            "myActionHTTPValue": "@action()['outputs']['body']['foo']",
            "transactionId": "@action()['inputs']['body']['bar']"
        }
    }
}
```

### <a name="extending-your-solutions"></a>扩展解决方案
可以从事件中心或存储将此遥测用于其他服务（如 [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)、[Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)和 [Power BI](https://powerbi.com)），以便实时监视集成工作流。

## <a name="next-steps"></a>后续步骤
* [逻辑应用的常见示例和方案](app-service-logic-examples-and-scenarios.md)
* [创建逻辑应用部署模板](app-service-logic-create-deploy-template.md)
* [企业集成功能](app-service-logic-enterprise-integration-overview.md)




<!--HONumber=Nov16_HO3-->


