---
title: "针对 Azure 活动日志警报调用 Webhook | Microsoft 文档"
description: "将活动日志事件路由到其他服务用于自定义操作。 例如发送短信、记录 bug，或者通过聊天/消息传送服务通知团队。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 08467aed4e1601b32598fc42515d9c38b601a9d4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="call-a-webhook-on-azure-activity-log-alerts"></a>针对 Azure 活动日志警报调用 Webhook
通过 webhook 可以将 Azure 警报通知路由到其他系统，以便进行后续处理或自定义操作。 可以针对警报使用 webhook，以将警报路由到可以发送短信、记录 Bug、通过聊天/消息通知团队，或执行任意数量的其他操作的服务。 本文介绍如何设置触发 Azure 活动日志警报时要调用的 Webhook。 此外，说明针对 Webhook 发出的 HTTP POST 的有效负载的大致形式。 有关 Azure 度量值警报的设置和架构的信息，[请参阅本页](insights-webhooks-alerts.md)。 还可以将活动日志警报设置为激活时发送电子邮件。

> [!NOTE]
> 此功能目前处于预览状态，会在以后删除。
>
>

可使用 [Azure PowerShell Cmdlets](insights-powershell-samples.md#create-metric-alerts)、[跨平台 CLI](insights-cli-samples.md#work-with-alerts) 或 [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) 设置活动日志警报。 目前无法使用 Azure 门户设置此类警报。

## <a name="authenticating-the-webhook"></a>对 webhook 进行身份验证
Webhook 可以使用以下任一方法进行身份验证：

1. **基于令牌的授权** - 保存的 Webhook URI 包含令牌 ID，例如 `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`。
2. **基本授权** - 保存的 Webhook URI 包含用户名和密码，例如 `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`。

## <a name="payload-schema"></a>负载架构
POST 操作对于所有基于活动日志的警报包含以下 JSON 有效负载和架构。 此架构类似于基于度量值的警报使用的架构。

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
| status |用于度量值警报。 对于活动日志警报始终设置为“已激活”。 |
| 上下文 |事件的上下文。 |
| activityLog | 事件的日志属性。|
| authorization |事件的 RBAC 属性。 这些通常包括“action”、“role”和“scope”。 |
| action | 由警报捕获的操作。 |
| 作用域 | 警报的范围（即 资源）。|
| channels | Operation |
| 声明 | 信息集合与声明有关。 |
| caller |执行操作（UPN 声明或 SPN 声明，具体取决于可用性）的用户的 GUID 或用户名。 对于某些系统调用可以为 null。 |
| correlationId |通常是字符串格式的 GUID。 具有属于同一较大操作的 correlationId 的事件，通常共享 correlationId。 |
| description |创建警报期间设置的警报说明。 |
| eventSource |生成事件的 Azure 服务或基础结构的名称。 |
| eventTimestamp |事件发生的时间。 |
| eventDataId |事件的唯一标识符。 |
| 级别 |以下值之一：“Critical”、“Error”、“Warning”、“Informational”和“Verbose”。 |
| operationName |操作的名称。 |
| operationId |通常是在与单个操作对应的事件之间共享的 GUID。 |
| resourceId |受影响资源的资源 ID。 |
| resourceGroupName |受影响资源的资源组的名称 |
| resourceProviderName |受影响资源的资源提供程序。 |
| status |字符串。 操作的状态。 常见值包括：“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”、“Resolved”。 |
| subStatus |通常包含对应 REST 调用的 HTTP 状态代码。 它还可能包含描述子状态的其他字符串。 常见子状态值包括：OK（HTTP 状态代码：200）、Created（HTTP 状态代码：201）、Accepted（HTTP 状态代码：202）、No Content（HTTP 状态代码：204）、Bad Request（HTTP 状态代码：400）、Not Found（HTTP 状态代码：404）、Conflict（HTTP 状态代码：409）、Internal Server Error（HTTP 状态代码：500）、Service Unavailable（HTTP 状态代码：503）、Gateway Timeout（HTTP 状态代码：504） |
| subscriptionId |Azure 订阅 ID。 |
| submissionTimestamp |处理请求的 Azure 服务生成事件的时间。 |
| resourceType | 已生成事件的资源类型。|
| 属性 |一组包含事件详细信息的 `<Key, Value>` 对（即 `Dictionary<String, String>`）。 |

## <a name="next-steps"></a>后续步骤
* [了解有关活动日志的更多信息](monitoring-overview-activity-logs.md)
* [Execute Azure Automation scripts (Runbooks) on Azure alerts](http://go.microsoft.com/fwlink/?LinkId=627081)（对 Azure 警报执行 Azure 自动化脚本 (Runbook)）
* [Use Logic App to send an SMS via Twilio from an Azure alert](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)（使用逻辑应用通过 Twilio 从 Azure 警报发送短信）。 本示例适用于度量值警报，但经过修改后可用于活动日志警报。
* [Use Logic App to send a Slack message from an Azure alert](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)（使用逻辑应用从 Azure 警报发送 Slack 消息）。 本示例适用于度量值警报，但经过修改后可用于活动日志警报。
* [Use Logic App to send a message to an Azure Queue from an Azure alert](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)（使用逻辑应用从 Azure 警报将消息发送到 Azure 队列）。 本示例适用于度量值警报，但经过修改后可用于活动日志警报。
