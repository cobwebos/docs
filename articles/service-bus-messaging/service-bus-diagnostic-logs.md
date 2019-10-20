---
title: Azure 服务总线诊断日志 | Microsoft Docs
description: 了解如何为 Azure 中的服务总线设置诊断日志。
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592414"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>为 Service Bus 启用诊断日志

开始使用 Azure 服务总线命名空间时，可能需要监视创建、删除或访问命名空间的方式和时间。 本文概述了所有可用的操作/诊断日志。

Azure 服务总线目前支持活动/操作日志，它们捕获在 Azure 服务总线命名空间上执行的**管理操作**。 具体而言，这些日志捕获操作类型，包括队列创建、所用的资源和操作状态。

## <a name="operational-logs-schema"></a>操作日志架构

所有日志都存储在以下2个位置的 JavaScript 对象表示法（JSON）格式中。

- **AzureActivity** -显示针对门户上的命名空间或通过 Azure 资源管理器模板部署执行的操作的日志。
- **AzureDiagnostics** -显示使用 API 或通过语言 SDK 上的管理客户端对命名空间执行的操作的日志。

运行日志 JSON 字符串包括下表列出的元素：

| 名称 | 描述 |
| ------- | ------- |
| ActivityId | 内部 ID，用于标识指定的活动 |
| EventName | 操作名称 |
| ResourceId | Azure 资源管理器资源 ID |
| SubscriptionId | 订阅 ID |
| EventTimeString | 操作时间 |
| EventProperties | 操作属性 |
| 状态 | 操作状态 |
| 调用方 | 操作的调用方（Azure 门户或管理客户端） |
| 类别 | OperationalLogs |

下面是运行日志 JSON 字符串的示例：

```json
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

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>哪些事件/操作在运行日志中捕获？

操作日志捕获对 Azure 服务总线命名空间执行的所有管理操作。 由于在 Azure 服务总线上进行的数据操作量很大，因此不会捕获数据操作。

> [!NOTE]
> 为了更好地跟踪数据操作，建议使用客户端跟踪。

在操作日志中捕获以下管理操作- 

| 范围 | Operation|
|-------| -------- |
| 命名空间 | <ul> <li> 创建命名空间</li> <li> 更新命名空间 </li> <li> 删除命名空间 </li>  </ul> | 
| 队列 | <ul> <li> 创建队列</li> <li> 更新队列</li> <li> 删除队列 </li> </ul> | 
| 主题 | <ul> <li> 创建主题 </li> <li> 更新主题 </li> <li> 删除主题 </li> </ul> |
| Subscription | <ul> <li> 创建订阅 </li> <li> 更新订阅 </li> <li> 删除订阅 </li> </ul> |

> [!NOTE]
> 目前，操作日志中不会跟踪**读取**操作。

## <a name="how-to-enable-operational-logs"></a>如何启用操作日志？

默认情况下禁用操作日志。 若要启用诊断日志，请按照以下步骤操作：

1. 在[Azure 门户](https://portal.azure.com)中，导航到 Azure 服务总线命名空间，然后在 "**监视**" 下，单击 "**诊断设置**"。

   ![在边栏选项卡中导航到诊断日志](./media/service-bus-diagnostic-logs/image1.png)

2. 单击 "**添加诊断设置**" 以配置诊断设置。  

   ![启用诊断日志](./media/service-bus-diagnostic-logs/image2.png)

3. 配置诊断设置
   1. 键入一个**名称**以标识诊断设置。
   2. 选取诊断目标。
      - 如果选择**存储帐户**，则需要配置将存储诊断的存储帐户。
      - 如果选取**事件中心**，则需要配置要将诊断设置流式传输到的相应事件中心。
      - 如果选择**Log Analytics**，则需要指定将向其发送诊断 Log Analytics 的实例。
    3. 请检查**OperationalLogs**。

       ![更改诊断日志的状态](./media/service-bus-diagnostic-logs/image3.png)

4. 单击“保存”。


新设置在大约 10 分钟后生效。 在此之后，日志将出现在“诊断日志”边栏选项卡上配置的存档目标中。

有关配置诊断的详细信息，请参阅 [Azure 诊断日志概述](../azure-monitor/platform/diagnostic-logs-overview.md)。

## <a name="next-steps"></a>后续步骤

请查看以下链接，详细了解服务总线：

* [服务总线简介](service-bus-messaging-overview.md)
* [服务总线入门](service-bus-dotnet-get-started-with-queues.md)
