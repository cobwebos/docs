---
title: 作为事件网格源的 Azure SignalR
description: 介绍为 Azure 事件网格中的 Azure SignalR 事件提供的属性
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2ac391f366c4b9a82741a1b6b3135f5d7b5fe331
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86106645"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>SignalR 服务的 Azure 事件网格事件架构

本文提供了 SignalR 服务事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。 它还提供了一个快速入门和教程的列表，这些快速入门和教程介绍如何使用 Azure SignalR 作为事件源。

## <a name="event-grid-event-schema"></a>事件网格事件架构

### <a name="available-event-types"></a>可用事件类型

SignalR 服务发出以下事件类型：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | 当连接客户端连接时引发。 |
| Microsoft.SignalRService.ClientConnectionDisconnected | 当客户端连接断开连接时引发。 |

### <a name="example-event"></a>示例事件

以下示例显示了客户端连接已连接事件的架构： 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

客户端连接断开连接事件的架构类似于： 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | string | 事件主题的发布者定义路径。 |
| eventType | string | 此事件源的一个注册事件类型。 |
| EventTime | string | 基于提供程序 UTC 时间的事件生成时间。 |
| id | string | 事件的唯一标识符。 |
| 数据 | object | SignalR 服务事件数据。 |
| dataVersion | string | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| timestamp | string | 基于提供程序 UTC 时间的事件生成时间。 |
| hubName | string | 客户端连接所属的中心。 |
| connectionId | string | 客户端连接的唯一标识符。 |
| userId | string | 声明中定义的用户标识符。 |
| errorMessage | string | 导致连接断开连接的错误。 |

## <a name="tutorials-and-how-tos"></a>教程和操作指南
|标题 | 说明 |
|---------|---------|
| [使用事件网格响应 Azure SignalR 服务事件](../azure-signalr/signalr-concept-event-grid-integration.md) | 概述 Azure SignalR 服务与事件网格的集成。 |
| [如何将 Azure SignalR 服务事件发送到事件网格](../azure-signalr/signalr-howto-event-grid-integration.md) | 演示如何通过事件网格将 Azure SignalR 服务事件发送到应用程序。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
