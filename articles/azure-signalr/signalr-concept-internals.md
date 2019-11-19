---
title: Azure SignalR 服务内部
description: 了解 Azure SignalR 服务内部机制、体系结构、连接以及数据的传输方式。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 62afa5ee6993aa1bb3c7b5926e5320ab1fa510a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157595"
---
# <a name="azure-signalr-service-internals"></a>Azure SignalR 服务内部

Azure SignalR 服务构建于 ASP.NET Core SignalR 框架的基础之上。 它还支持预览版功能 ASP.NET SignalR。

> 为了支持 ASP.NET SignalR，Azure SignalR 服务将在 ASP.NET Core 框架的顶层重新实现 ASP.NET SignalR 数据协议

只需更改少量的几行代码，即可轻松迁移本地 ASP.NET Core SignalR 应用程序以使用 SignalR 服务。

下图描绘了在应用程序服务器上使用 SignalR 服务时的典型体系结构。

本文还会介绍该服务与自承载式 ASP.NET Core SignalR 应用程序的差异。

![体系结构](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>服务器连接

自承载式 ASP.NET Core SignalR 应用程序服务器侦听客户端并直连到客户端。

使用 SignalR 服务时，应用程序服务器不再接受持久性客户端连接，而是：

1. 由 Azure SignalR 服务 SDK 为每个中心公开一个 `negotiate` 终结点。
1. 此终结点响应客户端的协商请求，并将客户端重定向到 SignalR 服务。
1. 客户端最终连接到 SignalR 服务。

有关详细信息，请参阅[客户端连接](#client-connections)。

启动应用程序服务器后： 
- 对于 ASP.NET Core SignalR，Azure SignalR 服务 SDK 将为 SignalR 服务开放每个中心的 5 个 WebSocket 连接。 
- 对于 ASP.NET SignalR，Azure SignalR 服务 SDK 将为 SignalR 服务开放每个中心的 5 个 WebSocket 连接，并为每个应用程序开放一个 WebSocket 连接。

5 个 WebSocket 连接是默认值，可在[配置](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount)中更改。

发往和发自客户端的消息将在这些连接中多路复用。

始终与 SignalR 服务保持这些连接。 如果网络问题导致服务器连接断开：
- 由此服务器连接提供服务的所有客户端将会断开连接（有关详细信息，请参阅[客户端与服务器之间的数据传输](#data-transmit-between-client-and-server)）；
- 服务器自动开始重新连接。

## <a name="client-connections"></a>客户端连接

使用 SignalR 服务时，客户端将连接到 SignalR 服务而不是应用程序服务器。
在客户端与 SignalR 服务之间建立持久性连接需要执行两个步骤。

1. 客户端向应用程序服务器发送协商请求。 应用程序服务器使用 Azure SignalR 服务 SDK 返回包含 SignalR 服务 URL 和访问令牌的重定向响应。

- 对于 ASP.NET Core SignalR，典型的重定向响应如下所示：
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- 对于 ASP.NET SignalR，典型的重定向响应如下所示：
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. 收到重定向响应后，客户端使用新的 URL 和访问令牌启动连接到 SignalR 服务的一般过程。

详细了解 ASP.NET Core SignalR 的[传输协议](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md)。

## <a name="data-transmit-between-client-and-server"></a>客户端与服务器之间的数据传输

客户端连接到 SignalR 服务后，服务运行时将查找服务器连接，以便为此客户端提供服务
- 此步骤只发生一次，它是客户端与服务器连接之间的一对一映射。
- 在客户端或服务器断开连接之前，SignalR 服务中会一直保持这种映射。

此时，应用程序服务器会收到一个事件，其中包含来自新客户端的信息。 在应用程序服务器中与客户端建立逻辑连接。 通过 SignalR 服务建立从客户端到应用程序服务器的数据通道。

SignalR 服务将客户端中的数据传输到配对的应用程序服务器。 应用程序服务器中的数据将发送到映射的客户端。

可以看出，Azure SignalR 服务本质上是应用程序服务器与客户端之间的逻辑传输层。 所有持久性连接将卸载到 SignalR 服务。
应用程序服务器只需处理中心类中的业务逻辑，而无需担心客户端连接。