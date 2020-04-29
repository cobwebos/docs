---
title: 作为事件网格源的 Azure SignalR
description: 介绍为 Azure 事件网格中的 Azure SignalR 事件提供的属性
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: babanisa
ms.openlocfilehash: e4ebae9597d750cea6f292655e9f03dd65ccc3f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133721"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>SignalR 服务的 Azure 事件网格事件架构

本文提供了 SignalR 服务事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。 它还提供了一个快速入门和教程列表，可使用 Azure SignalR 作为事件来源。

## <a name="event-grid-event-schema"></a>事件网格事件架构

### <a name="available-event-types"></a>可用事件类型

SignalR 服务发出以下事件类型：

| 事件类型 | 描述 |
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
| 主题 | 字符串 | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | 字符串 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| ID | 字符串 | 事件的唯一标识符。 |
| data | 对象 (object) | SignalR 服务事件数据。 |
| dataVersion | 字符串 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| timestamp | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| hubName | 字符串 | 客户端连接所属的中心。 |
| connectionId | 字符串 | 客户端连接的唯一标识符。 |
| userId | 字符串 | 声明中定义的用户标识符。 |
| errorMessage | 字符串 | 导致连接断开连接的错误。 |

## <a name="tutorials-and-how-tos"></a>教程和操作指南
|标题 | 说明 |
|---------|---------|
| [使用事件网格响应 Azure SignalR 服务事件](../azure-signalr/signalr-concept-event-grid-integration.md) | 概述 Azure SignalR 服务与事件网格的集成。 |
| [如何将 Azure SignalR 服务事件发送到事件网格](../azure-signalr/signalr-howto-event-grid-integration.md) | 演示如何通过事件网格将 Azure SignalR 服务事件发送到应用程序。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
