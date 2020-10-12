---
title: Azure 服务总线诊断日志 | Microsoft Docs
description: 本文概述了可用于 Azure 服务总线的所有操作和诊断日志。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f227f5a988ccd51425b4f43e87b4ed4d9af74e1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88064428"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>为服务总线启用诊断日志

开始使用 Azure 服务总线命名空间时，你可能想要监视命名空间的创建、删除或访问方式和时间。 本文概述所有可用的操作日志和诊断日志。

Azure 服务总线目前支持活动日志和操作日志，这些日志捕获针对 Azure 服务总线命名空间执行的管理操作。 具体而言，这些日志捕获操作类型，包括队列创建、所用的资源和操作状态。

## <a name="operational-logs-schema"></a>操作日志架构

所有日志均以 JavaScript 对象表示法 (JSON) 格式存储在以下两个位置：

- **AzureActivity**：显示通过 Azure 门户或 Azure 资源管理器模板部署对命名空间执行操作时生成的日志。
- **AzureDiagnostics**：显示使用 API 或通过语言 SDK 中的管理客户端对命名空间执行操作时生成的日志。

操作日志 JSON 字符串包含下表列出的元素：

| 名称 | 说明 |
| ------- | ------- |
| ActivityId | 内部 ID，用于标识指定的活动 |
| EventName | 操作名称 |
| ResourceId | Azure Resource Manager 资源 ID |
| SubscriptionId | 订阅 ID |
| EventTimeString | 操作时间 |
| EventProperties | 操作属性 |
| 状态 | 操作状态 |
| 调用方 | 操作的调用方（Azure 门户或管理客户端） |
| Category | OperationalLogs |

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

## <a name="events-and-operations-captured-in-operational-logs"></a>在操作日志中捕获的事件和操作

操作日志捕获针对 Azure 服务总线命名空间执行的所有管理操作。 针对 Azure 服务总线执行的数据操作量很大，因此不会捕获数据操作。

> [!NOTE]
> 为了帮助你更好地跟踪数据操作，我们建议使用客户端跟踪。

在操作日志中捕获以下管理操作： 

| 作用域 | 操作|
|-------| -------- |
| 命名空间 | <ul> <li> 创建命名空间</li> <li> 更新命名空间 </li> <li> 删除命名空间 </li> <li> 更新命名空间 SharedAccess 策略 </li> </ul> | 
| 队列 | <ul> <li> 创建队列</li> <li> 更新队列</li> <li> 删除队列 </li> <li> 自动删除 - 删除队列 </li> </ul> | 
| 主题 | <ul> <li> 创建主题 </li> <li> 更新主题 </li> <li> 删除主题 </li> <li> 自动删除 - 删除主题 </li> </ul> |
| 订阅 | <ul> <li> 创建订阅 </li> <li> 更新订阅 </li> <li> 删除订阅 </li> <li> 自动删除 - 删除订阅 </li> </ul> |

> [!NOTE]
> 目前，不会在操作日志中跟踪“读取”操作。

## <a name="enable-operational-logs"></a>启用操作日志

操作日志默认已禁用。 若要启用诊断日志，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，转到你的 Azure 服务总线命名空间，然后在“监视”下选择“诊断设置”。

   ![“诊断设置”链接](./media/service-bus-diagnostic-logs/image1.png)

1. 在“诊断设置”窗格中，选择“添加诊断设置”。  

   ![“添加诊断设置”链接](./media/service-bus-diagnostic-logs/image2.png)

1. 执行以下操作来配置诊断设置：

   a. 在“名称”框中，输入诊断设置的名称。  

   b. 为诊断日志选择以下三个目标之一：  
   - 如果选择“存档到存储帐户”，则需要配置用于存储诊断日志的存储帐户。  
   - 如果选择“流式传输到事件中心”，则需要配置要将诊断日志流式传输到的事件中心。
   - 如果选择“发送到 Log Analytics”，则需要指定要将诊断发送到的 Log Analytics 实例。  

   c. 选中“OperationalLogs”复选框。

    ![“诊断设置”窗格](./media/service-bus-diagnostic-logs/image3.png)

1. 选择“保存” 。

新设置将在大约 10 分钟后生效。 日志将显示在“诊断日志”窗格中配置的存档目标中。

有关配置诊断设置的详细信息，请参阅 [Azure 诊断日志概述](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="next-steps"></a>后续步骤

若要详细了解服务总线，请参阅：

* [服务总线简介](service-bus-messaging-overview.md)
* [服务总线入门](service-bus-dotnet-get-started-with-queues.md)