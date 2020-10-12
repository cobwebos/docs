---
title: 排查 Azure 服务总线中的 AMQP 错误 | Microsoft Docs
description: 提供使用 Azure 服务总线时可能收到的 AMQP 错误以及这些错误的原因的列表。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 88b10940e0b910f50e6ccf7f8c53134fa7f0ba2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88064343"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Azure 服务总线中的 AMQP 错误
本文提供了在 Azure 服务总线中使用 AMQP 时所收到的一些错误。 这些错误都是服务的标准行为。 可以通过在连接/链路上发出发送/接收调用来避免这些错误，这会自动重新创建连接/链路。

## <a name="link-is-closed"></a>链路已关闭 
当 AMQP 连接和链路处于活动状态，但 10 分钟内没有使用该链路发出调用（例如发送或接收）时，你将看到以下错误。 因此，该链路将关闭。 连接仍处于打开状态。

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>连接已关闭
当连接中的所有链路由于 5 分钟内没有活动（空闲）且未创建新链路而关闭时，你会在 AMQP 连接上看到以下错误。

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>未创建链路 
如果创建了新的 AMQP 连接，但在创建 AMQP 连接后的 1 分钟内未创建链路，则会看到此错误。

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>后续步骤

若要详细了解 AMQP 和服务总线，请单击以下链接：

* [服务总线 AMQP 概述]
* [AMQP 1.0 协议指南]
* [适用于 Windows Server 的服务总线中的 AMQP]

[服务总线 AMQP 概述]: service-bus-amqp-overview.md
[AMQP 1.0 协议指南]: service-bus-amqp-protocol-guide.md
[适用于 Windows Server 的服务总线中的 AMQP]: /previous-versions/service-bus-archive/dn282144(v=azure.100)