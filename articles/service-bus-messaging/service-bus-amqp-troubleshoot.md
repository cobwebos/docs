---
title: 对 Azure 服务总线中的 AMQP 错误进行故障排除 |Microsoft Docs
description: 提供了一系列 AMQP 错误，可能会接收时使用 Azure 服务总线，并会导致这些错误。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58910040"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Azure 服务总线中 AMQP 错误
本文提供了一些用于 Azure 服务总线使用 AMQP 时收到的错误。 它们是服务的所有标准行为。 您可以避免使用它们连接/链接，这会自动重新创建连接/链接上，从而发送/接收调用。

## <a name="link-is-closed"></a>链接已关闭 
AMQP 连接进行连接和链接是处于活动状态但不是包括调用时，将显示以下错误 （例如，发送或接收） 都使用 10 分钟的链接。 因此，链接已关闭。 连接仍处于打开状态。

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>连接已关闭
当因为没有任何活动 （空闲），并且未在 5 分钟内创建的新链接已关闭的连接中的所有链接时，AMQP 连接上看到以下错误。

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>不创建链接 
当创建新的 AMQP 连接但未在 AMQP 连接创建的 1 分钟内创建的链接时看到此错误。

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>后续步骤

若要详细了解 AMQP 和服务总线，请单击以下链接：

* [服务总线 AMQP 概述]
* [AMQP 1.0 协议指南]
* [适用于 Windows Server 的服务总线中 AMQP]

[服务总线 AMQP 概述]: service-bus-amqp-overview.md
[AMQP 1.0 协议指南]: service-bus-amqp-protocol-guide.md
[适用于 Windows Server 的服务总线中 AMQP]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
