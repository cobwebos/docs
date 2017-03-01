---
title: "了解如何在逻辑应用中使用 Azure 服务总线连接器 | Microsoft Docs"
description: "使用 Azure App Service 创建逻辑应用。 连接到 Azure 服务总线以发送和接收消息。 可执行发送到队列、发送到主题、从队列接收和从订阅接收等操作。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 1e23402cbc63aeb262bfb471745589cc0bbd734f


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Azure 服务总线连接器入门
连接到 Azure 服务总线以发送和接收消息。 可执行发送到队列、发送到主题、从队列接收和从订阅接收等操作。

若要使用 [任何连接器](apis-list.md)，首先需要创建逻辑应用。 可通过 [立即创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md) 开始操作。

## <a name="connect-to-service-bus"></a>连接到服务总线
需要先创建到任何服务的连接，然后逻辑应用才能访问该服务。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>使用服务总线触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>使用服务总线操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="technical-details"></a>技术详细信息
下面详细介绍了此连接支持的触发器、操作和响应。

### <a name="service-bus-triggers"></a>服务总线触发器
服务总线具有以下触发器：  

| 触发器 | 说明 |
| --- | --- |
| [在队列中收到消息时](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue) |此操作在队列中收到消息时触发流。 |
| [在主题订阅中收到消息时](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription) |此操作在主题订阅中收到消息时触发流。 |

### <a name="service-bus-actions"></a>服务总线操作
服务总线具有以下操作：

| 操作 | 说明 |
| --- | --- |
| [发送消息](connectors-create-api-servicebus.md#send-message) |此操作向队列或主题发送消息。 |

### <a name="action-and-trigger-details"></a>操作和触发器详细信息
下面详细介绍了此连接器的操作和触发器及其响应。

#### <a name="send-message"></a>发送消息
| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| ContentData* |内容 |消息的内容。 |
| ContentType |内容类型 |消息内容的内容类型。 |
| 属性 |属性 |每个中转属性的键值对。 |
| entityName* |队列/主题名称 |队列或主题的名称。 |

这些高级参数也可用：

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| MessageId |消息 ID |服务总线可用于标识重复消息的用户定义值（如果启用）。 |
| 如果 |如果 |要发送到的地址。 |
| ReplyTo |回复 |要回复的队列地址。 |
| ReplyToSessionId |回复会话 ID |要回复的会话标识符。 |
| 标签 |标签 |应用程序特定的标签。 |
| ScheduledEnqueueTimeUtc |ScheduledEnqueueTimeUtc |将消息添加到队列的日期和时间（采用 UTC）。 |
| SessionId |会话 ID |会话标识符。 |
| CorrelationId |相关性 ID |相关性标识符。 |
| TimeToLive |生存时间 |消息有效的持续时间，以刻度为单位。 持续时间开始于邮件发送到服务总线时。 |

* 指示属性是必需的。

#### <a name="when-a-message-is-received-in-a-queue"></a>在队列中收到邮件时
| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| queueName* |队列名称 |队列名称。 |

* 指示属性是必需的。

##### <a name="output-details"></a>输出详细信息
ServiceBusMessage：此对象具有服务总线消息的内容和属性。

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ContentData |字符串 |消息的内容。 |
| ContentType |字符串 |消息内容的内容类型。 |
| 属性 |对象 |每个中转属性的键值对。 |
| MessageId |字符串 |服务总线可用于标识重复消息的用户定义值（如果启用）。 |
| 如果 |字符串 |发送到地址。 |
| ReplyTo |字符串 |要回复的队列地址。 |
| ReplyToSessionId |字符串 |要回复的会话标识符。 |
| 标签 |字符串 |应用程序特定的标签。 |
| ScheduledEnqueueTimeUtc |字符串 |将消息添加到队列的日期和时间（采用 UTC）。 |
| SessionId |字符串 |会话标识符。 |
| CorrelationId |字符串 |相关性标识符。 |
| TimeToLive |字符串 |消息有效的持续时间，以刻度为单位。 持续时间开始于邮件发送到服务总线时。 |

#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>当在主题订阅中收到邮件时
| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| topicName* |主题名称 |主题名称。 |
| subscriptionName* |主题订阅名称 |主题订阅名称。 |

* 指示属性是必需的。

##### <a name="output-details"></a>输出详细信息
ServiceBusMessage：此对象具有服务总线消息的内容和属性。

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ContentData |字符串 |消息的内容。 |
| ContentType |字符串 |消息内容的内容类型。 |
| 属性 |对象 |每个中转属性的键值对。 |
| MessageId |字符串 |服务总线可用于标识重复消息的用户定义值（如果启用）。 |
| 如果 |字符串 |发送到地址。 |
| ReplyTo |字符串 |要回复的队列地址。 |
| ReplyToSessionId |字符串 |要回复的会话标识符。 |
| 标签 |字符串 |应用程序特定的标签。 |
| ScheduledEnqueueTimeUtc |字符串 |将消息添加到队列的日期和时间（采用 UTC）。 |
| SessionId |字符串 |会话标识符。 |
| CorrelationId |字符串 |相关性标识符。 |
| TimeToLive |字符串 |消息有效的持续时间，以刻度为单位。 持续时间开始于邮件发送到服务总线时。 |

### <a name="http-responses"></a>HTTP 响应
之前的操作和触发器可以返回以下一个或多个 HTTP 状态代码：

| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 202 |已接受 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误。 |
| default |操作失败。 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。




<!--HONumber=Jan17_HO3-->


