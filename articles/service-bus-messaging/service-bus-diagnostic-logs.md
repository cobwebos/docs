---
title: "Azure 服务总线诊断日志 | Microsoft 文档"
description: "了解如何分析 Microsoft Azure 中的服务总线的诊断日志。"
keywords: 
documentationcenter: 
services: service-bus-messaging
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/17/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 90321171586110a3b60c3df5b749003ebbf70ec9
ms.openlocfilehash: 70205b33e9d52e41f5c1a637fee4da192e2a971a
ms.lasthandoff: 02/22/2017


---
# <a name="service-bus-diagnostic-logs"></a>服务总线诊断日志

## <a name="introduction"></a>介绍
服务总线公开两种类型的日志： 
* [活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)：始终启用，提供针对作业执行的操作的深入信息；
* [诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)：用户可配置的日志，提供作业发生的所有情况的更多深入信息，包括从创建作业开始，到更新、运行作业，直到删除作业为止的所有信息；

## <a name="how-to-enable-diagnostic-logs"></a>如何启用诊断日志
诊断日志默认已**禁用**。 若要启用诊断日志，请执行下列步骤：

登录到 Azure 门户并导航到流式处理作业边栏选项卡，然后使用“监视”下面的“诊断日志”边栏选项卡。

![在边栏选项卡中导航到诊断日志](./media/service-bus-diagnostic-logs/image1.png)  

然后单击“启用诊断”链接

![启用诊断日志](./media/service-bus-diagnostic-logs/image2.png)

在打开的诊断中，将状态更改为“打开”。

![更改诊断日志的状态](./media/service-bus-diagnostic-logs/image3.png)

配置所需的存档目标（存储帐户、事件中心、Log Analytics）并选择想要收集的日志类别（“执行”、“创作”）。 然后保存新的诊断配置。

保存后，配置将在大约 10 分钟后生效，在此之后，日志将开始出现在配置的存档目标中，可在“诊断日志”边栏选项卡中看到：

[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)页中提供了有关配置诊断的详细信息。

## <a name="diagnostic-logs-schema"></a>诊断日志架构

所有日志以 JSON 格式存储，每个项包含以下格式的字符串字段。

### <a name="operation-logs"></a>操作日志

OperationalLogs 捕获服务总线操作期间发生的日志 - 特别是队列创建等操作类型、所使用的资源和操作的状态。

Name | 说明
------- | -------
ActivityId | 用于跟踪目的的内部 ID
EventName | 操作名称             
resourceId | ARM 资源 ID
SubscriptionId | 订阅 ID
EventTimeString | 操作时间
EventProperties | 操作属性
状态 | 操作状态
调用方 | 操作的调用方（门户或管理客户端）
category | OperationalLogs

#### <a name="example-operation-log"></a>示例操作日志

```json
Example: 
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create Queue",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>后续步骤
* [服务总线简介](service-bus-messaging-overview.md)
* [服务总线入门](service-bus-create-namespace-portal.md)

