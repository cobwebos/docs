---
title: 在 Azure Monitor 中对经典指标警报调用 Webhook
description: 了解如何将 Azure 指标警报重新路由到其他非 Azure 系统。
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: alerts
ms.openlocfilehash: 0677c7a0521fe1f63c9c2c9fce65d8dbd8e6d5c4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826904"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>在 Azure Monitor 中对经典指标警报调用 Webhook

可以使用 Webhook 将 Azure 警报通知路由到其他系统，以便进行后续处理或自定义操作。 可以针对警报使用 Webhook，以将警报路由到可以发送短信的服务，以记录 Bug、通过聊天/消息服务通知团队，或进行各种其他操作。 

本文介绍如何针对 Azure 指标警报设置 Webhook。 此外，还说明向 Webhook 发出的 HTTP POST 的有效负载的大致形式。 有关 Azure 活动日志警报（事件警报）的设置和架构的信息，[针对 Azure 活动日志警报调用 Webhook](alerts-log-webhook.md)。

Azure 警报使用 HTTP POST 将警报内容以 JSON 格式发送到创建警报时提供的 Webhook URI。 本文中稍后将定义架构。 此 URI 必须是有效的 HTTP 或 HTTPS 终结点。 激活警报时，Azure 会针对每个请求发布一个条目。

## <a name="configure-webhooks-via-the-azure-portal"></a>通过 Azure 门户配置 Webhook
若要添加或更新 Webhook URI，请在 [Azure 门户](https://portal.azure.com/)中转到“创建/更新警报”  。

![“添加警报规则”窗格](./media/alerts-webhooks/Alertwebhook.png)

还可以使用 [Azure PowerShell cmdlet](../samples/powershell-samples.md#create-metric-alerts)、[跨平台 CLI](../samples/cli-samples.md#work-with-alerts) 或 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) 将警报配置为发布到 Webhook URI。

## <a name="authenticate-the-webhook"></a>对 webhook 进行身份验证
Webhook 可使用基于令牌的身份验证进行身份验证。 保存的 Webhook URI 具有令牌 ID。 例如： `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>负载架构
POST 操作对于所有基于指标的警报包含以下 JSON 有效负载和架构：

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| 字段 | 必需 | 一组固定值 | 注释 |
|:--- |:--- |:--- |:--- |
| 状态 |Y |Activated, Resolved |基于设置的条件的警报的状态。 |
| 上下文 |Y | |警报上下文。 |
| timestamp |Y | |触发警报的时间。 |
| id |Y | |每个警报规则都有一个唯一 ID。 |
| name |Y | |警报名称。 |
| description |Y | |警报的说明。 |
| conditionType |Y |“Metric”、“Event” |支持两种类型的警报：指标和事件。 指标警报基于指标条件。 事件警报基于活动日志中的事件。 使用此值可检查警报是基于指标还是基于事件。 |
| condition |Y | |要基于 **conditionType** 值检查的特定字段。 |
| metricName |用于指标警报 | |定义规则监视对象的指标的名称。 |
| metricUnit |用于指标警报 |“Bytes”、“BytesPerSecond”、“Count”、“CountPerSecond”、“Percent”、“Seconds” |指标中允许使用的单位。 请参阅[允许的值](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx)。 |
| metricValue |用于指标警报 | |导致警报的实际度量值。 |
| 阈值 |用于指标警报 | |会激活警报的阈值。 |
| windowSize |用于指标警报 | |用于根据阈值监视警报活动的时间段。 此值必须介于 5 分钟到 1 天之间。 此值必须采用 ISO 8601 持续时间格式。 |
| timeAggregation |用于指标警报 |“Average”、“Last”、“Maximum”、“Minimum”、“None”、“Total” |随着时间推移，收集的数据应如何组合。 默认值为 Average。 请参阅[允许的值](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx)。 |
| operator |用于指标警报 | |用于将当前指标数据与所设阈值进行比较的运算符。 |
| subscriptionId |Y | |Azure 订阅 ID。 |
| resourceGroupName |Y | |受影响资源的资源组名称。 |
| resourceName |Y | |受影响资源的资源名称。 |
| resourceType |Y | |受影响资源的资源类型。 |
| ResourceId |Y | |受影响资源的资源 ID。 |
| resourceRegion |Y | |受影响资源的区域或位置。 |
| portalLink |Y | |指向门户资源摘要页的直接链接。 |
| properties |N |可选 |包含有关事件的详细信息的一组键/值对。 例如，`Dictionary<String, String>`。 properties 字段是可选的。 在自定义 UI 或基于逻辑应用的工作流中，用户可以输入键/值对，该键/值对可通过有效负载进行传递。 将自定义属性传递回 Webhook 的替代方法是通过 Webhook URI 本身（作为查询参数）。 |

> [!NOTE]
> 只能使用 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) 设置“属性”字段。
>
>

## <a name="next-steps"></a>后续步骤
* 通过[将 Azure 警报与 PagerDuty 集成](https://go.microsoft.com/fwlink/?LinkId=627080)视频了解有关 Azure 警报和 Webhook 的详细信息。
* 了解如何[对 Azure 警报执行 Azure 自动化脚本 (Runbook)](https://go.microsoft.com/fwlink/?LinkId=627081)。
* 了解如何[使用逻辑应用通过 Twilio 从 Azure 警报发送短信](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)。
* 了解如何[使用逻辑应用从 Azure 警报发送 Slack 消息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)。
* 了解如何[使用逻辑应用从 Azure 警报将消息发送到 Azure 队列](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)。

