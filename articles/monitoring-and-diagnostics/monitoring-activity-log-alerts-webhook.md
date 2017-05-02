# <a name="webhooks-for-azure-activity-log-alerts"></a>Azure 活动日志警报的 webhook
作为操作组定义的一部分，可以将 webhook 终结点配置为接收活动日志警报通知。 通过 webhook 可以将这些通知路由到其他系统，以便进行后续处理或自定义操作。 本文介绍针对 webhook 发出的 HTTP POST 的有效负载的大致形式。

有关 Azure 活动日志警报的设置和架构的信息，[请查看本页](monitoring-activity-log-alerts.md)。

有关操作组的设置和架构的信息，[请查看本页](monitoring-action-groups.md)

## <a name="authenticating-the-webhook"></a>对 webhook 进行身份验证
使用基于令牌的授权可对 webhook 进行身份验证 - 保存的 webhook URI 包含令牌 ID，例如 `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>负载架构
根据有效负载的 data.context.activityLog.eventSource 字段，POST 操作中包含的 JSON 有效负载会有所不同。

###<a name="common"></a>常见
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
###<a name="administrative"></a>管理
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
###<a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                },
            }
        },
        "properties": {}
    }
}
```

有关服务运行状况通知活动日志警报的特定架构详细信息，请[单击此处](monitoring-service-notifications.md)；有关其他所有活动日志警报的特定架构详细信息，请[单击此处](monitoring-overview-activity-logs.md)

| 元素名称 | 说明 |
| --- | --- |
| status |用于度量值警报。 对于活动日志警报始终设置为“已激活”。 |
| 上下文 |事件的上下文。 |
| resourceProviderName |受影响资源的资源提供程序。 |
| conditionType |始终为“事件”。 |
| 名称 |警报规则的名称。 |
| id |警报的资源 ID。 |
| description |创建警报期间设置的警报说明。 |
| subscriptionId |Azure 订阅 ID。 |
| timestamp |处理请求的 Azure 服务生成事件的时间。 |
| resourceId |受影响资源的资源 ID。 |
| resourceGroupName |受影响资源的资源组的名称 |
| 属性 |一组包含事件详细信息的 `<Key, Value>` 对（即 `Dictionary<String, String>`）。 |
| event |包含有关事件的元数据的元素。 |
| authorization |事件的 RBAC 属性。 这些属性通常包括“action”、“role”和“scope”。 |
| category |事件的类别。 支持的值包括：Administrative、Alert、Security、ServiceHealth、Recommendation。 |
| caller |执行操作的用户的电子邮件地址（基于可用性的 UPN 声明或 SPN 声明）。 对于某些系统调用可以为 null。 |
| correlationId |通常是字符串格式的 GUID。 具有属于同一较大操作的 correlationId 的事件，通常共享 correlationId。 |
| eventDescription |事件的静态文本说明。 |
| eventDataId |事件的唯一标识符。 |
| eventSource |生成事件的 Azure 服务或基础结构的名称。 |
| httpRequest |请求通常包括“clientRequestId”、“clientIpAddress”和 HTTP“method”（例如 PUT）。 |
| 级别 |以下值之一：“Critical”、“Error”、“Warning”、“Informational”和“Verbose”。 |
| operationId |通常是在与单个操作对应的事件之间共享的 GUID。 |
| operationName |操作的名称。 |
| 属性 |事件的属性。 |
| status |字符串。 操作的状态。 常见值包括：“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”、“Resolved”。 |
| subStatus |通常包含对应 REST 调用的 HTTP 状态代码。 它还可能包含描述子状态的其他字符串。 常见子状态值包括：OK（HTTP 状态代码：200）、Created（HTTP 状态代码：201）、Accepted（HTTP 状态代码：202）、No Content（HTTP 状态代码：204）、Bad Request（HTTP 状态代码：400）、Not Found（HTTP 状态代码：404）、Conflict（HTTP 状态代码：409）、Internal Server Error（HTTP 状态代码：500）、Service Unavailable（HTTP 状态代码：503）、Gateway Timeout（HTTP 状态代码：504） |

## <a name="next-steps"></a>后续步骤
* [了解有关活动日志的更多信息](monitoring-overview-activity-logs.md)
* [Execute Azure Automation scripts (Runbooks) on Azure alerts](http://go.microsoft.com/fwlink/?LinkId=627081)（对 Azure 警报执行 Azure 自动化脚本 (Runbook)）
* [Use Logic App to send an SMS via Twilio from an Azure alert](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)（使用逻辑应用通过 Twilio 从 Azure 警报发送短信）。 本示例适用于度量值警报，但经过修改后可用于活动日志警报。
* [Use Logic App to send a Slack message from an Azure alert](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)（使用逻辑应用从 Azure 警报发送 Slack 消息）。 本示例适用于度量值警报，但经过修改后可用于活动日志警报。
* [Use Logic App to send a message to an Azure Queue from an Azure alert](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)（使用逻辑应用从 Azure 警报将消息发送到 Azure 队列）。 本示例适用于度量值警报，但经过修改后可用于活动日志警报。
