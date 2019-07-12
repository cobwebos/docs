---
title: 响应 Azure SignalR 服务事件
description: 使用 Azure 事件网格订阅 Azure SignalR 服务事件。
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: azure-signalr
ms.openlocfilehash: 02f88c5953d499b30f2ea3408318f70a72f42b0f
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789184"
---
# <a name="reacting-to-azure-signalr-service-events"></a>响应 Azure SignalR 服务事件

Azure SignalR 服务事件允许应用程序响应客户端连接已连接或断开连接使用新式无服务器体系结构。 为此，它无需复杂的代码或高价低效的轮询服务。  相反，可以通过 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)向订阅者（如 [Azure Functions](https://azure.microsoft.com/services/functions/) 或 [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)，甚至是你的自定义 HTTP 侦听器）推送事件，且仅需为已使用的内容付费。

Azure SignalR 服务事件可靠地发送到事件网格服务提供可靠的传递服务添加到您通过丰富的应用程序重试策略和死信传递。 若要了解详细信息，请参阅[事件网格消息传送和重试](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)。

![事件网格模型](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>无服务器状态
Azure SignalR 服务事件只能处于活动状态的客户端连接时在无服务器的状态。 通常情况下，如果客户端不会将路由到中心服务器，它将进入无服务器的状态。 仅当中心，客户端连接连接到，没有集线器服务器时，经典模式下工作。 但是，若要避免一些问题，建议进行无服务器模式。 若要了解有关服务模式的更多详细信息，请参阅[如何选择服务模式](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)。

## <a name="available-azure-signalr-service-events"></a>可用 Azure SignalR 服务事件
事件网格使用[事件订阅](../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。 Azure SignalR 服务事件订阅支持两种类型的事件：  

|事件名称|描述|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|当连接的客户端连接时引发。|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|当客户端连接已断开连接时引发。|

## <a name="event-schema"></a>事件架构
Azure SignalR 服务事件包含响应数据中的更改所需的所有信息。 您可以标识 Azure SignalR 服务使用 eventType 属性的事件启动与"Microsoft.SignalRService"。 有关使用情况的事件网格事件属性的其他信息记录在[事件网格事件架构](../event-grid/event-schema.md)。  

下面是客户端连接已连接事件的一个示例：
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

有关详细信息，请参阅[SignalR 服务事件架构](../event-grid/event-schema-azure-signalr.md)。

## <a name="next-steps"></a>后续步骤

了解有关事件网格的详细信息，并尝试一下 Azure SignalR 服务事件：

> [!div class="nextstepaction"]
> [请尝试使用 Azure SignalR 服务的示例事件网格集成](./signalr-howto-event-grid-integration.md)
> [关于事件网格](../event-grid/overview.md)
