---
title: "使用 Webhook 为现有的问题管理系统配置运行状况通知 | Microsoft Docs"
description: "获取有关发送到现有问题管理系统的服务运行状况事件的个性化通知。"
author: shawntabrizi
manager: scotthit
editor: 
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: b6a5f61f61675b825dcfe9c706c80944f5890538
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>使用 Webhook 为现有的问题管理系统配置运行状况通知

本文介绍如何配置服务运行状况警报，以便通过 Webhook 将数据发送到现有的通知系统。

现在，你可以配置服务运行状况警报，在 Azure 服务事件影响到自己时，通过短信或电子邮件获得通知。
不过，你可能已经有了现成的可以使用的外部通知系统。
本文档介绍 Webhook 有效负载的最重要部分，并介绍如何创建自定义警报，在服务问题影响自己时获得通知。

若要使用预配置的集成，请了解如何执行以下操作：
* [使用 ServiceNow 配置警报](service-health-alert-webhook-servicenow.md)
* [使用 PagerDuty 配置警报](service-health-alert-webhook-pagerduty.md)
* [使用 OpsGenie 配置警报](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>使用服务运行状况 Webhook 有效负载配置自定义通知
若要设置自己的自定义 Webhook 集成，需分析在服务运行状况通知期间发送的 JSON 有效负载。

查看[此处的示例](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md)，了解 `Service Health` Webhook 有效负载。

可以通过查看 `context.eventSource == "ServiceHealth"` 来确定其是否为服务运行状况警报。 在这里，与引入最相关的属性为：
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-azure-service-health-for-an-incident"></a>针对某个事件，创建 Azure 服务运行状况的直接链接
可以通过生成专用 URL，在台式或移动设备上创建个性化 Azure 服务运行状况事件的直接链接。 使用 `trackingId` 以及 `subscriptionId` 的头三个和最后三个数字来生成该链接：
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

例如，如果 `subscriptionId` 为 `bba14129-e895-429b-8809-278e836ecdb3`，`trackingId` 为 `0DET-URB`，则个性化 Azure 服务运行状况 URL 为：

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>使用级别来表示问题的严重性
有效负载中的 `level` 属性可以是 `Informational`、`Warning`、`Error`、`Critical` 中的任意一种（从最不严重到最严重排列）。

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>分析受影响的服务，了解事件的全貌
服务运行状况警报可以将多个区域和服务的问题告知你。 若要获取完整详细信息，需分析 `impactedServices` 的值。
其中的内容为 [JSON 转义](http://json.org/)字符串，当取消转义时，包含另一可以进行定期分析的 JSON 对象。

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

这表明，澳大利亚东部和东南部存在“警报和指标”问题，澳大利亚东南部存在“应用服务”问题。


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>通过 HTTP POST 请求测试 Webhook 集成
1. 创建要发送的服务运行状况有效负载。 可以在 [Azure 活动日志警报的 Webhook](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md) 中找到示例服务运行状况 Webhook 有效负载。

2. 按如下所示创建 HTTP POST 请求：

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. 应收到 `2XX - Successful` 响应。

4. 转到 [PagerDuty](https://www.pagerduty.com/)，确认集成已设置成功。

## <a name="next-steps"></a>后续步骤
- 查看[活动日志警报 webhook 架构](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md)。 
- 了解[服务运行状况通知](../monitoring-and-diagnostics/monitoring-service-notifications.md)。
- 详细了解[操作组](../monitoring-and-diagnostics/monitoring-action-groups.md)。