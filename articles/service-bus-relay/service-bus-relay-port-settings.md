---
title: "Azure 服务总线 WCF 中继端口设置 | Microsoft Docs"
description: "有关服务总线 WCF 中继端口值的详细信息。"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: f222caa272b86ff42873df09561ca4f6c6e6aa26
ms.openlocfilehash: ba499a98960b0cf2f865e2d349003cd8cb65b526


---

# <a name="azure-relay-port-settings"></a>Azure 中继端口设置

下表描述了服务总线 WCF 中继端口值绑定的所需配置。

## <a name="ports"></a>端口
  
|绑定|传输安全|端口|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding 类](/dotnet/api/microsoft.servicebus.basichttprelaybinding)（客户端）|是|HTTPS| 
| |" |否|HTTP|  
|[BasicHttpRelayBinding 类](/dotnet/api/microsoft.servicebus.basichttprelaybinding)（服务）|任一个|9351/HTTP|  
|[NetEventRelayBinding 类](/dotnet/api/microsoft.servicebus.neteventrelaybinding)（客户端）|是|9351/HTTPS|  
||" |否|9350/HTTP|  
|[NetEventRelayBinding 类](/dotnet/api/microsoft.servicebus.neteventrelaybinding)（服务）|任一个|9351/HTTP|  
|[NetTcpRelayBinding 类](/dotnet/api/microsoft.servicebus.nettcprelaybinding)（客户端/服务）|任一个|5671/9352/HTTP（9352/9353，如果使用混合）|  
|[NetOnewayRelayBinding 类](/dotnet/api/microsoft.servicebus.netonewayrelaybinding)（客户端）|是|9351/HTTPS|  
||" |否|9350/HTTP|  
|[NetOnewayRelayBinding 类](/dotnet/api/microsoft.servicebus.netonewayrelaybinding)（服务）|任一个|9351/HTTP|  
|[WebHttpRelayBinding 类](/dotnet/api/microsoft.servicebus.webhttprelaybinding)（客户端）|是|HTTPS|  
||" |否|HTTP|  
|[WebHttpRelayBinding 类](/dotnet/api/microsoft.servicebus.webhttprelaybinding)（服务）|任一个|9351/HTTP|  
|[WS2007HttpRelayBinding 类](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding)（客户端）|是|HTTPS|  
||" |否|HTTP|  
|[WS2007HttpRelayBinding 类](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding)（服务）|任一个|9351/HTTP|

## <a name="next-steps"></a>后续步骤
若要了解有关服务总线消息传送的详细信息，请参阅以下主题。

* [服务总线基础知识](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)
* [如何使用 Service Bus 队列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)



<!--HONumber=Nov16_HO4-->


