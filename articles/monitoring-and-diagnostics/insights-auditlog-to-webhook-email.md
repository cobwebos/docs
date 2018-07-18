---
title: 针对 Azure 活动日志警报（经典）调用 Webhook
description: 了解如何将活动日志事件路由到其他服务用于自定义操作。 例如发送短信、记录 bug，或者通过聊天或消息传送服务通知团队。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: e825d0f2487c20c8c7f3d210d7180b07742d7173
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262454"
---
# <a name="call-a-webhook-on-an-azure-activity-log-alert"></a>针对 Azure 活动日志警报调用 Webhook
可以使用 Webhook 将 Azure 警报通知路由到其他系统，以便进行后续处理或自定义操作。 可以针对警报使用 Webhook，以将警报路由到可以发送短信的服务，以记录 Bug、通过聊天/消息服务通知团队，或进行各种其他操作。 还可以将活动日志警报设置为激活时发送电子邮件。

本文介绍如何设置触发 Azure 活动日志警报时要调用的 Webhook。 此外，说明针对 Webhook 发出的 HTTP POST 的有效负载的大致形式。 有关 Azure 指标警报的设置和架构的信息，请参阅[针对 Azure 指标警报配置 Webhook](insights-webhooks-alerts.md)。 

> [!NOTE]
> 目前，支持对 Azure 活动日志警报调用 webhook 的功能处于预览状态。
>
>

可使用 [Azure PowerShell Cmdlets](insights-powershell-samples.md#create-metric-alerts)、[跨平台 CLI](insights-cli-samples.md#work-with-alerts) 或 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) 设置活动日志警报。 目前，Azure 门户不可用于设置活动日志警报。

## <a name="authenticate-the-webhook"></a>对 webhook 进行身份验证
Webhook 可以使用以下任一方法进行身份验证：

* **基于令牌的身份验证**。 保存的 Webhook URI 具有令牌 ID。 例如： `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
* **基本授权**。 保存的 webhook URI 具有用户名和密码。 例如：`https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>负载架构
POST 操作对于所有基于活动日志的警报包含以下 JSON 有效负载和架构。 此架构类似于基于指标的警报使用的架构。

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| 元素名称 | 说明 |
| --- | --- |
| status |用于度量值警报。 对于活动日志警报，始终设置为“已激活”。|
| 上下文 |事件的上下文。 |
| activityLog | 事件的日志属性。|
| authorization |事件的基于角色的访问控制 (RBAC) 属性。 这些属性通常包括 **action**、**role** 和 **scope**。 |
| action | 由警报捕获的操作。 |
| 作用域 | 警报的范围（即资源）。|
| channels | 操作。 |
| 声明 | 信息集合与声明有关。 |
| caller |执行操作（UPN 声明或 SPN 声明，具体取决于可用性）的用户的 GUID 或用户名。 对于某些系统调用可以为 null 值。 |
| correlationId |通常是字符串格式的 GUID。 具有属于同一较大操作的 **correlationId** 的事件。 它们通常具有相同的 **correlationId** 值。 |
| description |创建警报时设置警报说明。 |
| eventSource |生成事件的 Azure 服务或基础结构的名称。 |
| eventTimestamp |事件发生的时间。 |
| eventDataId |事件的唯一标识符。 |
| 级别 |以下值之一：“Critical”、“Error”、“Warning”、“Informational”或“Verbose”。 |
| operationName |操作的名称。 |
| operationId |通常在事件之间共享的 GUID。 GUID 通常对应于单个操作。 |
| resourceId |受影响资源的资源 ID。 |
| resourceGroupName |受影响资源的资源组名称。 |
| resourceProviderName |受影响资源的资源提供程序。 |
| status |一个字符串值，指示操作的状态。 常见值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| subStatus |通常包含对应 REST 调用的 HTTP 状态代码。 它还可能包含描述子状态的其他字符串。 常见子状态值包括 OK（HTTP 状态代码：200）、Created（HTTP 状态代码：201）、Accepted（HTTP 状态代码：202）、No Content（HTTP 状态代码：204）、Bad Request（HTTP 状态代码：400）、Not Found（HTTP 状态代码：404）、Conflict（HTTP 状态代码：409）、Internal Server Error（HTTP 状态代码：500）、Service Unavailable（HTTP 状态代码：503）和 Gateway Timeout（HTTP 状态代码：504）。 |
| subscriptionId |Azure 订阅 ID。 |
| submissionTimestamp |处理请求的 Azure 服务生成事件的时间。 |
| resourceType | 已生成事件的资源类型。|
| 属性 |包含有关事件的详细信息的一组键/值对。 例如，`Dictionary<String, String>`。 |

## <a name="next-steps"></a>后续步骤
* 详细了解[活动日志](monitoring-overview-activity-logs.md)。
* 了解如何[对 Azure 警报执行 Azure 自动化脚本 (Runbook)](http://go.microsoft.com/fwlink/?LinkId=627081)。
* 了解如何[使用逻辑应用通过 Twilio 从 Azure 警报发送短信](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)。 本示例适用于指标警报，但经过修改后可用于活动日志警报。
* 了解如何[使用逻辑应用从 Azure 警报发送 Slack 消息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)。 本示例适用于指标警报，但经过修改后可用于活动日志警报。
* 了解如何[使用逻辑应用从 Azure 警报将消息发送到 Azure 队列](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)。 本示例适用于指标警报，但经过修改后可用于活动日志警报。
