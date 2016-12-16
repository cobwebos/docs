---
title: "Log Analytics 警报 REST API"
description: "Log Analytics 警报 REST API 允许在 Operations Management Suite (OMS) 中创建和管理警报。  本文提供了用于执行不同操作的 API 和几个示例的详细信息。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 53a7be4d213f3f4c6d01b95355543fc9cd55717f


---
# <a name="log-analytics-alert-rest-api"></a>Log Analytics 警报 REST API
Log Analytics 警报 REST API 允许在 Operations Management Suite (OMS) 中创建和管理警报。  本文提供了用于执行不同操作的 API 和几个示例的详细信息。

Log Analytics 搜索 REST API 为 RESTful，可通过 Azure Resource Manager REST API 访问。 在本文档中你会看到使用 [ARMClient](https://github.com/projectkudu/ARMClient)（可简化 Azure Resource Manager API 调用的开放源命令行工具）从 PowerShell 命令行访问 API 的示例。 ARMClient 和 PowerShell 的使用是访问 Log Analytics 搜索 API 的许多选项之一。 使用这些工具可以利用 RESTful Azure Resource Manager API 对 OMS 工作区进行调用并执行它们所包含的搜索命令。 API 将以 JSON 格式输出搜索结果，从而允许你以编程方式使用许多不同的方法来搜索结果。

## <a name="prerequisites"></a>先决条件
目前，仅可以使用 Log Analytics 中已保存的搜索来创建警报。  有关详细信息，请参阅[日志搜索 REST API](log-analytics-log-search-api.md)。

## <a name="schedules"></a>计划
已保存的搜索可以有一个或多个计划。 计划定义搜索的运行频率以及标识条件的时间间隔。
计划具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| 时间间隔 |搜索的运行频率。 以分钟为度量单位。 |
| QueryTimeSpan |计算条件的时间间隔。 必须等于或大于间隔。 以分钟为度量单位。 |
| 版本 |正在使用的 API 版本。  目前应始终设置为 1。 |

例如，考虑一个间隔为 15 分钟和时间跨度为 30 分钟的事件查询。 在这种情况下，将每隔 15 分钟运行一次查询，如果条件在超过 30 分钟后继续解析为 true，则会触发警报。

### <a name="retrieving-schedules"></a>检索计划
使用 Get 方法为检索已保存搜索的所有计划。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

结合使用 Get 方法和计划 ID 检索已保存搜索的特定计划。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

下面是一个响应计划的示例。

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>创建计划
结合使用 Put 方法和唯一计划 ID 创建一个新计划。  请注意，两个计划 ID 不能一样，即使它们与不同的已保存搜索相关联。  在 OMS 控制台中创建计划时，将为计划 ID 创建一个 GUID。

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>编辑计划
结合使用 Put 方法和相同已保存搜索的现有计划 ID 来修改该计划。  请求正文必须包含该计划的 etag。

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>删除计划
结合使用 Delete 方法和计划 ID 删除计划。

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>操作
一个计划可以有多个操作。 某项操作可能会定义一个或多个进程，以执行例如发送邮件或启动 Runbook 等操作，或者它可能会定义确定搜索结果与某些条件的匹配时间的阈值。  某些操作将同时定义这两者，以便达到阈值时执行这些进程。

所有操作具有下表中的属性。  不同类型的警报具有不同的其他属性，如下所述。

| 属性 | 说明 |
|:--- |:--- |
| 类型 |操作的类型。  目前可能的值为警报和 Webhook。 |
| Name |警报的显示名称。 |
| 版本 |正在使用的 API 版本。  目前应始终设置为 1。 |

### <a name="retrieving-actions"></a>检索操作
使用 Get 方法检索计划的所有操作。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

结合使用 Get 方法和计划 ID 检索计划的特定操作。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>创建或编辑操作
结合使用 Put 方法和计划唯一的操作 ID 可创建新操作。  在 OMS 控制台中创建操作时，GUID 是针对操作 ID。

结合使用 Put 方法和相同已保存搜索的现有操作 ID 来修改该计划。  请求正文必须包含该计划的 etag。

创建新操作的请求格式因操作类型而异，因此在以下各节中提供了这些示例。

### <a name="deleting-actions"></a>删除操作
结合使用 Delete 方法和操作 ID 可删除操作。

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>警报操作
一个计划应具有一个且只能有一个警报操作。  警报操作具有下表中的的一个或多个部分。  下面对各部分进行了详细描述。

| 部分 | 说明 |
|:--- |:--- |
| 阈值 |运行操作时的条件。 |
| EmailNotification |向多个收件人发送邮件。 |
| 补救 |在 Azure 自动化中启动 Runbook，以尝试纠正发现的问题。 |

#### <a name="thresholds"></a>阈值
一项警报操作应具有一个且只能有一个阈值。  当已保存搜索的结果与该搜索关联的操作中的阈值匹配时，将运行该操作中的任何其他进程。  操作也可以只包含一个阈值，以便它可以与不包含阈值的其他类型的操作一起使用。

阈值具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| 运算符 |阈值比较运算符。 <br> gt = 大于 <br> lt = 小于 |
| 值 |阈值的数值。 |

例如，考虑一个间隔为 15 分钟、时间跨度为 30 分钟并且阈值大于 10 的事件查询。 在这种情况下，将每隔 15 分钟运行一次查询，如果返回超过 30 分钟时间跨度创建的 10 个事件，则会触发警报。

下面是响应仅具备一个阈值的操作的示例。  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

结合使用 Put 方法和唯一操作 ID 可为计划创建新阈值操作。  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

结合使用 Put 方法和现有操作 ID 可修改计划的阈值操作。  请求正文必须包含操作的 etag。

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>电子邮件通知
向一个或多个收件人发送邮件的电子邮件通知。  它们具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| 收件人 |邮件地址列表。 |
| 使用者 |邮件主题。 |
| 附件 |当前不支持附件，因此该值始终为“None”。 |

下面是响应仅具备一个阈值的电子邮件通知操作的示例。  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

结合使用 Put 方法和唯一操作 ID 可为计划创建新电子邮件操作。  下面的示例创建了具备一个阈值的电子邮件通知，因此当已保存搜索的结果超出阈值时会发送邮件。

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

结合使用 Put 方法和现有操作 ID 可修改计划的电子邮件操作。  请求正文必须包含操作的 etag。

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>修正操作
修正在 Azure 自动化中启动 Runbook，尝试纠正警报发现的问题。  必须为用于修正操作的 Runbook 创建 Webhook，然后在 WebhookUri 属性中指定 URI。  使用 OMS 控制台创建此操作时，将自动为 Runbook 创建新的 Webhook。

修正具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| RunbookName |Runbook 的名称。 它必须与 OMS 工作区自动化解决方案中配置的自动化帐户中已发布的 Runbook 相匹配。 |
| WebhookUri |Webhook 的 URI。 |
| Expiry |Webhook 的到期日期和时间。  如果 Webhook 没有到期，那么这可以是任何有效的未来日期。 |

下面是响应仅具备一个阈值的修正操作的示例。

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

结合使用 Put 方法和唯一操作 ID 可为计划创建新的修正操作。  下面的示例创建了具备一个阈值的修正，因此当已保存搜索的结果超出阈值时便会启动 Runbook。

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

结合使用 Put 方法和现有操作 ID 可修改计划的修正操作。  请求正文必须包含操作的 etag。

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>示例
下面是创建新电子邮件警报的完整示例。  这将创建一个新计划以及包含阈值和电子邮件的操作。

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $workspaceId    = "MyWorkspace"
    $searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Webhook 操作
Webhook 操作通过调用 URL 和提供要发送的负载（可选）启动进程。  Webhook 操作与修正操作类似，不同之处在于它们是用于 Webhook，可能调用 Azure 自动化 Runbook 之外的进程。  此外，它们还提供其他选项来提供要发送到远程进程的负载。

Webhook 操作没有阈值，但应添加到具有带阈值的警报操作的计划中。  可以添加多个 Webhook 操作，达到该阈值时将运行这些操作。

Webhook 操作具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| WebhookUri |邮件主题。 |
| CustomPayload |发送到 Webhook 的自定义负载。  格式将取决于 Webhook 的期望。 |

下面是响应 Webhook 操作和包含阀值的关联警报操作的示例。

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>创建或编辑 Webhook 操作
结合使用 Put 方法和唯一操作 ID 为计划创建新的 Webhook 操作。  下面的示例创建了 Webhook 操作和包含一个阈值的警报操作，因此已保存搜索的结果超出阈值时将触发 Webhook。

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

结合使用 Put 方法和现有操作 ID 修改计划的 Webhook 操作。  请求正文必须包含操作的 etag。

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>后续步骤
* 在 Log Analytics 中使用 [REST API 执行日志搜索](log-analytics-log-search-api.md)。




<!--HONumber=Nov16_HO3-->


