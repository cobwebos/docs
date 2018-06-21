---
title: 如何使用 Azure Monitor 警报和操作组触发复杂操作
description: 了解如何创建逻辑应用操作处理 Azure Monitor 警报。
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: eafb2bcf0175190748c9dd020051cbebfcaee1fd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263878"
---
# <a name="create-a-logic-app-action"></a>创建逻辑应用操作
## <a name="overview"></a>概述 ##
触发 Azure Monitor 警报时，它将调用[操作组](monitoring-action-groups.md)。 通过操作组可触发一个或多个操作，就警报进行通知甚至修正。

本文介绍如何设置和触发逻辑应用，在警报触发时在 Microsoft Teams 中创建对话。

常规过程：

-   为相应的警报类型创建逻辑应用

-   将相应警报类型的架构导入逻辑应用

-   定义逻辑应用行为

-   将逻辑应用的 HTTP 终结点复制到 Azure 操作组

如果希望逻辑应用执行其他操作，过程于此类似。

## <a name="create-an-activity-log-alert--administrative"></a>创建活动日志警报 – 管理

1.  单击 Azure 门户左上角的“创建资源”按钮。

2.  搜索并选择“逻辑应用”。 单击“创建”  按钮。

3.  为逻辑应用命名以及选择一个资源组等。

    ![创建逻辑应用对话框](media/monitoring-action-groups/create-logic-app-dialog.png "创建逻辑应用对话框")

4.  单击“创建”按钮，创建逻辑应用。 逻辑应用创建完成时，将显示一个弹出窗口。 单击“启动资源”按钮，打开逻辑应用设计器。

5.  选择触发器“当收到 HTTP 请求时”。

    ![逻辑应用触发器](media/monitoring-action-groups/logic-app-triggers.png "逻辑应用触发器")

6.  选择“编辑”，更改 HTTP 请求触发器

    ![HTTP 请求触发器形状](media/monitoring-action-groups/http-request-trigger-shape.png "HTTP 请求触发器形状")

7.  选择“使用示例有效负载生成架构”。

    ![使用示例有效负载按钮](media/monitoring-action-groups/use-sample-payload-button.png "使用示例有效负载按钮")

8.  将以下示例架构复制并粘贴到对话框中。

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. 逻辑应用设计器将显示注释，提醒发送到逻辑应用的请求必须将 Content-Type 标头设置为 application/json。 继续并关闭对话框。 Azure Monitor 警报将正确执行此操作。

    ![Content-Type 标头](media/monitoring-action-groups/content-type-header.png "Content-Type 标头")

10. 依次选择“+ 新建步骤”、“添加操作”。

    ![添加操作](media/monitoring-action-groups/add-action.png "添加操作")

11. 搜索并选择 Microsoft Teams 连接器。 选择“Microsoft Teams – 发布消息”操作。

    ![Microsoft Teams 操作](media/monitoring-action-groups/microsoft-teams-actions.png "Microsoft Teams 操作")

12. 配置 Microsoft Teams 操作。 逻辑应用设计器将要求对 Office365 帐户进行身份验证。 选择要向其发送消息的“团队 Id”和“通道 Id”。

13. 结合使用静态文本和对动态内容\<字段\>的引用来配置“消息”。 将以下文本剪切并粘贴到“消息”字段。

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    然后，搜索\<字段\>并将其替换为使用相同名称的动态内容标记。

    **[注意！]** 有两个名为“status”的动态字段。 将这两个“status”字段都添加到消息。 使用 activityLog 属性包中那一个，并删除另一个。 如果将鼠标悬停在“status”字段上，将看到完全限定的字段引用，如屏幕截图所示

    ![ 操作 - 发布消息](media/monitoring-action-groups/teams-action-post-message.png " 操作 - 发布消息")

14. 单击设计器顶部的“保存”按钮，保存逻辑应用

15. 单击 HTTP 请求形状将其展开。 复制 HTTP POST URL。

    ![HTTP POST URL](media/monitoring-action-groups/http-post-url.png "HTTP POST URL")

16. 打开现有操作组并添加一个操作，从而引用逻辑应用。 如果没有现有操作组，请参阅 <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups> 创建一个操作组。 切勿忘记保存更改。

    ![更新操作组](media/monitoring-action-groups/update-action-group.png "更新操作组")

下次警报调用操作组时，将调用逻辑应用。

## <a name="create-a-service-health-alert"></a>创建服务运行状况警报

服务运行状况项是活动日志的一部分，因此创建过程与以下更改相似

1.  步骤 1 至步骤 7 相同。
2.  步骤 8 中，为 HTTP 触发器使用以下示例架构。

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

3.  步骤 9 - 10 相同。
4.  从步骤 11 开始，需按以下流程操作。
5.  单击“+ 新建步骤”按钮，然后选择“”添加条件。 设置以下条件，确保逻辑应用仅在输入数据匹配这些值时执行
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - version == 0.1.1

    ![“服务运行状况有效负载条件”](media/monitoring-action-groups/service-health-payload-condition.png "服务运行状况有效负载条件")

6. 在“如果为 true”条件下，使用上一示例中的步骤 11 – 13 添加 Microsoft Teams 操作。

7. 使用 HTML 和 [动态内容] 的组合定义消息。 将下面的内容复制并粘贴到消息字段。 使用名称相同的动态内容标记替换 [incidentType]、[trackingID]、 [title] 和 [communication] 字段

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    ![“服务运行状况 true 条件下的发布操作”](media/monitoring-action-groups/service-health-true-condition-post-action.png "服务运行状况 true 条件下的发布操作")

8. 对于“如果为 false”条件，请提供有用的消息

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    ![“服务运行状况 false 条件下的发布操作”](media/monitoring-action-groups/service-health-false-condition-post-action.png "服务运行状况 false 条件下的发布操作")

9.  请按照上一示例的步骤 15 – 16 保存逻辑应用并更新操作组

## <a name="metric-alert"></a>指标警报

1.  步骤 1 至 7 与第一个示例相同
2.  步骤 8 中，为 HTTP 触发器使用以下示例架构。

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

3.  步骤 9 - 10 相同。
4.  从步骤 11 开始，需按以下流程操作。
5.  单击“+ 新建步骤”按钮，然后选择“”添加条件。 设置以下条件，确保逻辑应用仅在输入数据匹配这些值时执行

    - schemaId == AzureMonitorMetricAlert
    - version == 2.0

    ![“指标警报有效负载条件”](media/monitoring-action-groups/metric-alert-payload-condition.png "指标警报有效负载条件")

6.  在“如果为 true”条件中，添加“每个”形状和 Microsoft Teams 操作，并使用 HTML 和 [动态内容] 组合定义消息

    ![“指标警报 true 件下的发布操作”](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "指标警报 true 件下的发布操作")

7.  在“如果为 false”形状中，定义 Microsoft Teams 操作，传达指标警报不匹配逻辑应用的期望并包含 JSON 有效负载。 请留意如何引用 json() 表达式中的 triggerBody 动态内容。

    ![“指标警报 false 条件下的发布操作”](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "指标警报 false 条件下的发布操作")

8.  请按照第一个示例的步骤 15 – 16 保存逻辑应用并更新操作组

## <a name="next-steps"></a>后续步骤 ##
* 获取[活动日志警报概述](monitoring-overview-alerts.md)，了解如何接收警报。  
* 了解如何[配置每次发布服务运行状况通知时的警报](monitoring-activity-log-alerts-on-service-notifications.md)。
* 详细了解[操作组](monitoring-action-groups.md)