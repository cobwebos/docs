---
title: "Azure 服务总线消息处理体系结构概述 | Microsoft 文档"
description: "介绍 Azure 服务总线的消息和中继处理体系结构。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: baf94c2d-0e58-4d5d-a588-767f996ccf7f
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: ced46c64c1c105aa987759e05ab3680bc399f9a0
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---
# <a name="service-bus-architecture"></a>服务总线体系结构
本文介绍 Azure 服务总线的消息处理体系结构。

## <a name="service-bus-scale-units"></a>服务总线缩放单位
服务总线按 *缩放单位*进行组织。 缩放单位是部署单位，包含运行服务所需的全部组件。 每个区域部署一个或多个服务总线缩放单位。

一个服务总线命名空间映射到一个缩放单位。 缩放单位负责处理各种类型的服务总线实体：中继和中转消息传送实体（队列、主题、订阅）。 服务总线缩放单位由以下组件构成：

* **一组网关节点。** 网关节点对传入请求进行身份验证并处理中继请求。 每个网关节点都有一个公共 IP 地址。
* **一组消息代理节点。** 消息代理节点处理有关消息实体的请求。
* **一个网关存储。** 网关存储保存此缩放单位中定义的每个实体的数据。 网关存储在 SQL Azure 数据库顶层实施。
* **多个消息存储。** 消息存储可保留在此缩放单位中定义的所有队列、主题和订阅的消息。 它还包含所有订阅数据。 除非启用了[分区消息实体](service-bus-partitioning.md)，否则队列或主题将映射到一个消息存储。 订阅是存储在与其父主题相同的消息存储中。 除了服务总线 [高级消息传送](service-bus-premium-messaging.md)外，消息存储在 SQL Azure 数据库顶层实施。

## <a name="containers"></a>容器
将为每个消息实体分配特定的容器。 容器是一种逻辑构造，它刚好使用一个消息存储来存储此容器的所有相关数据。 将为每个容器分配一个消息代理节点。 通常，容器比消息代理节点要多。 因此，每个消息代理节点将加载多个容器。 消息代理节点的容器分配经过适当的组织，可以均衡加载所有消息代理节点。 如果加载模式发生更改（例如其中一个容器变得十分繁忙），或消息代理节点暂时不可用，则会在消息代理节点之间重新分配容器。

## <a name="processing-of-incoming-messaging-requests"></a>处理传入消息请求
当客户端向服务总线发送请求时，Azure 负载均衡器将其路由到任何一个网关节点。 网关节点将为请求授权。 如果请求涉及某个消息传送实体（队列、主题、订阅），网关节点将在网关存储中查找该实体，并确定该实体位于哪个消息存储中。 然后，它将查询哪些消息代理节点目前正在为此容器提供服务，并将请求发送到该消息代理节点。 消息代理节点将处理请求并更新容器存储中的实体状态。 然后，消息代理节点向网关节点发送响应，而网关节点向发出原始请求的客户端发送相应的响应。

![处理传入消息请求](./media/service-bus-architecture/ic690644.png)

## <a name="processing-of-incoming-relay-requests"></a>处理传入中继请求
当客户端向 [Azure 中继](/azure/service-bus-relay/)服务发送请求时，Azure 负载均衡器将其路由到任何一个网关节点。 如果请求为侦听请求，网关节点将创建新的中继。 如果请求是对特定中继的连接请求，网关节点会将连接请求转发给拥有中继的网关节点。 拥有中继的网关节点向侦听客户端发送会合请求，要求侦听器与接收连接请求的网关节点创建一个临时通道。

建立中继连接后，客户端可以通过用于会合的网关节点交换消息。

![处理传入 WCF 中继请求](./media/service-bus-architecture/ic690645.png)

## <a name="next-steps"></a>后续步骤
阅读服务总线体系结构的概述后，请参阅以下链接了解详细信息。

* [服务总线消息传送概述](service-bus-messaging-overview.md)
* [Azure 中继概述](../service-bus-relay/relay-what-is-it.md)
* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [使用服务总线队列的队列消息解决方案](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)



