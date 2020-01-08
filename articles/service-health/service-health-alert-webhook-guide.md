---
title: 使用 webhook 发送 Azure 服务运行状况通知
description: 向现有问题管理系统发送有关服务运行状况事件的个性化通知。
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 95926185057d9fc1177b974fe76b2da18ebfc124
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551669"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>使用 webhook 为问题管理系统配置运行状况通知

本文介绍如何配置 Azure 服务运行状况警报，以便通过 webhook 将数据发送到现有的通知系统。

你可以配置服务运行状况警报，以便在 Azure 服务事件影响到你时通过短信或电子邮件通知你。

但你可能已经有了更喜欢使用的现有外部通知系统。 本文确定 webhook 有效负载的最重要部分。 还介绍了如何创建自定义警报，以便在出现相关服务问题时通知你。

如果要使用预配置的集成，请参阅：
* [使用 ServiceNow 配置警报](service-health-alert-webhook-servicenow.md)
* [使用 PagerDuty 配置警报](service-health-alert-webhook-pagerduty.md)
* [使用 OpsGenie 配置警报](service-health-alert-webhook-opsgenie.md)

**观看介绍性视频：**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>使用服务运行状况 webhook 有效负载配置自定义通知
若要设置自己的自定义 webhook 集成，需要分析通过服务运行状况通知发送的 JSON 有效负载。

请参阅 `ServiceHealth` webhook 有效负载[的示例](../azure-monitor/platform/activity-log-alerts-webhook.md)。

可以通过查看 `context.eventSource == "ServiceHealth"`来确认它是服务运行状况警报。 以下属性是最相关的属性：
- **Activitylog.xml。状态**
- **Activitylog.xml。**
- **Activitylog.xml。**
- **Activitylog.xml..。**
- **Activitylog.xml. impactStartTime。**
- **Activitylog.xml 通信的数据**
- **Activitylog.xml. impactedServices。**
- **Activitylog.xml. trackingId。**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>为事件创建服务运行状况仪表板的链接
你可以通过生成专用 URL 来创建到桌面或移动设备上的服务运行状况仪表板的直接链接。 按以下格式使用*subscriptionId*的*trackingId*和前三个和后三位数字：

https<i></i>：//App.azure.com/h/ *&lt;trackingId&gt;* / *&lt;订阅 id 的前三位和后三位数&gt;*

例如，如果你的*subscriptionId*为 Bba14129-e895-429b-8809-278e836ecdb3 并且*trackingId*为 0DET-URB，则服务运行状况 URL 为：

https<i></i>：//app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>使用级别来检测问题的严重性
从最低到最高的严重性，负载中的**level**属性可以是*信息*、*警告*、*错误*或*严重*。

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>分析受影响的服务以确定事件范围
服务运行状况警报可以通知你跨多个区域和服务的问题。 若要获取完整的详细信息，需要分析 `impactedServices`的值。

内部的内容是一个转义的[json](https://json.org/)字符串，在未转义时，它包含可定期进行分析的另一个 JSON 对象。 例如：

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

将变为：

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

此示例显示的问题：
- 澳大利亚东部和澳大利亚东南部的 "警报 & 指标"。
- 澳大利亚东南部的 "应用服务"。

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>通过 HTTP POST 请求测试 webhook 集成

执行以下步骤:

1. 创建要发送的服务运行状况有效负载。 请参阅[Azure 活动日志警报的 webhook](../azure-monitor/platform/activity-log-alerts-webhook.md)上的示例服务运行状况 webhook 负载。

1. 按如下所示创建 HTTP POST 请求：

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   应该会收到 "2XX-成功" 响应。

1. 转到 [PagerDuty](https://www.pagerduty.com/)，确认集成已设置成功。

## <a name="next-steps"></a>后续步骤
- 查看[活动日志警报 webhook 架构](../azure-monitor/platform/activity-log-alerts-webhook.md)。 
- 了解[服务运行状况通知](../azure-monitor/platform/service-notifications.md)。
- 详细了解[操作组](../azure-monitor/platform/action-groups.md)。