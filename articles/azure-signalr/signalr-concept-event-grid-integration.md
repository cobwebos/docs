---
title: 响应 Azure SignalR 服务事件
description: 使用 Azure 事件网格订阅 Azure SignalR 服务事件。 这些事件可触发其他下游服务。
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "74158192"
---
# <a name="reacting-to-azure-signalr-service-events"></a>响应 Azure SignalR 服务事件

Azure SignalR 服务事件允许应用程序使用新式无服务器体系结构响应客户端连接的连接或断开连接。 为此，它无需复杂的代码或高价低效的轮询服务。  相反，可以通过 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)向订阅者（如 [Azure Functions](https://azure.microsoft.com/services/functions/) 或 [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)，甚至是你的自定义 HTTP 侦听器）推送事件，且仅需为已使用的内容付费。

Azure SignalR 服务事件会可靠地发送到事件网格服务，该服务通过丰富的重试策略和死信传递向应用程序提供可靠的传递服务。 若要了解详细信息，请参阅[事件网格消息传递和重试](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)。

![事件网格模型](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>无服务器状态
Azure SignalR 服务事件处于活动状态的前提是客户端连接处于无服务器状态。 一般说来，客户端在不路由到中心服务器的情况下会进入无服务器状态。 经典模式适用的前提是客户端连接连接到的中心没有中心服务器。 但是，我们仍建议使用无服务器模式，以免出现某种问题。 若要了解服务模式的更多详细信息，请参阅[如何选择服务模式](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)。

## <a name="available-azure-signalr-service-events"></a>可用的 Azure SignalR 服务事件
事件网格使用[事件订阅](../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。 Azure SignalR 服务事件订阅支持两种类型的事件：  

|事件名称|说明|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|建立客户端连接时出现。|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|断开客户端连接时出现。|

## <a name="event-schema"></a>事件架构
Azure SignalR 服务事件包含响应数据中的更改所需的所有信息。 可以识别 Azure SignalR 服务事件，该事件的 eventType 属性以“Microsoft.SignalRService”开头。 关于事件网格事件属性使用情况的其他信息，请参阅[事件网格事件架构](../event-grid/event-schema.md)文档。  

下面是“客户端连接已连接”事件的示例：
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

有关详细信息，请参阅 [SignalR 服务事件架构](../event-grid/event-schema-azure-signalr.md)。

## <a name="next-steps"></a>后续步骤

详细了解事件网格并尝试使用 Azure SignalR 服务事件：

> [!div class="nextstepaction"]
> [尝试将事件网格与 Azure SignalR 服务进行示例性的集成](./signalr-howto-event-grid-integration.md)
> [关于事件网格](../event-grid/overview.md)
