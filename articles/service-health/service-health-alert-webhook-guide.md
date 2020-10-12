---
title: 通过 Webhook 发送 Azure 服务运行状况通知
description: 将有关服务运行状况事件的个性化通知发送到现有的问题管理系统。
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 05b0572c89a29fddc881f9977ee437d1319e6254
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518922"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>使用 Webhook 为问题管理系统配置运行状况通知

本文介绍如何配置 Azure 服务运行状况警报，以通过 Webhook 将数据发送到现有的通知系统。

你可以配置服务运行状况警报，以便在 Azure 服务事件影响到自己时，通过短信或电子邮件收到通知。

但是，你可能已经部署了你偏好使用的现有外部通知系统。 本文将介绍 Webhook 有效负载的最重要部分， 并介绍如何创建自定义警报，以便在服务问题影响到自己时收到通知。

若要使用预配置的集成，请参阅：
* [使用 ServiceNow 配置警报](service-health-alert-webhook-servicenow.md)
* [使用 PagerDuty 配置警报](service-health-alert-webhook-pagerduty.md)
* [使用 OpsGenie 配置警报](service-health-alert-webhook-opsgenie.md)

**观看介绍视频：**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>使用服务运行状况 Webhook 有效负载配置自定义通知
若要设置自己的自定义 Webhook 集成，需分析通过服务运行状况通知发送的 JSON 有效负载。

请参阅[示例](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` Webhook 有效负载。

可以通过查看 `context.eventSource == "ServiceHealth"` 来确认它是否为服务运行状况警报。 以下属性是最相关的属性：
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>针对事件创建服务运行状况仪表板的链接
通过生成专用 URL，可在桌面或移动设备上创建服务运行状况仪表板的直接链接。 使用 *trackingId* 以及采用以下格式的 *subscriptionId* 的前三个和最后三个数字：

https<i></i>://app.azure.com/h/ *&lt;trackingId&gt;* / *&lt;subscriptionId 的前三个和最后三个数字&gt;*

例如，如果 *subscriptionId* 为 bba14129-e895-429b-8809-278e836ecdb3，*trackingId* 为 0DET-URB，则服务运行状况 URL 为：

https<i></i>://app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>使用级别来检测问题的严重性
有效负载中的 **level** 属性可以是 *Informational*、*Warning*、*Error* 或 *Critical*（从最不严重到最严重排列）。

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>分析受影响的服务以确定事件范围
服务运行状况警报可告知你有关多个区域和服务的问题。 若要获取完整详细信息，需分析 `impactedServices` 的值。

其中的内容为转义的 [JSON](https://json.org/) 字符串，取消转义时，包含另一个可以定期分析的 JSON 对象。 例如：

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

 变为：

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

此示例显示：
- 澳大利亚东部和澳大利亚东南部的“警报和指标”问题。
- 澳大利亚东南部的“应用服务”问题。

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>通过 HTTP POST 请求测试 Webhook 集成

执行以下步骤:

1. 创建要发送的服务运行状况有效负载。 查看 [Azure 活动日志警报的 Webhook](../azure-monitor/platform/activity-log-alerts-webhook.md) 中的示例服务运行状况 Webhook 有效负载。

1. 按如下所示创建 HTTP POST 请求：

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   应会收到“2XX - 成功”响应。

1. 转到 [PagerDuty](https://www.pagerduty.com/)，确认集成已设置成功。

## <a name="next-steps"></a>后续步骤
- 查看[活动日志警报 webhook 架构](../azure-monitor/platform/activity-log-alerts-webhook.md)。 
- 了解[服务运行状况通知](./service-notifications.md)。
- 详细了解[操作组](../azure-monitor/platform/action-groups.md)。
