---
title: Azure SignalR 服务中的消息和连接
description: 有关 Azure SignalR 服务中的消息和连接的重要概念概述。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 5483e10e817ce8a0a7e7c82d817b7bdbbdd9176b
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853443"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Azure SignalR 服务中的消息和连接

Azure SignalR 服务的计费模型基于连接数和消息数。 本文介绍消息和连接数目的定义方式，以及它们如何影响计费。


## <a name="message-formats"></a>消息格式 

Azure SignalR 服务支持的格式与 ASP.NET Core SignalR 相同：[JSON](https://www.json.org/) 和 [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)。

## <a name="message-size"></a>消息大小

Azure SignalR 服务对消息大小不施加限制。

大消息将拆分为小于 2 KB 的若干较小消息，并单独传输。 消息拆分与组合由 SDK 进行处理， 无需开发人员的干预。

大消息确实会对消息传送性能造成负面影响。 请尽量使用小消息，并通过测试来为每种使用方案确定最佳消息大小。

## <a name="how-messages-are-counted-for-billing"></a>消息如何影响计费

计费时，我们只会统计来自 Azure SignalR 服务的出站消息。 将忽略客户端与服务器之间的 Ping 消息。

大于 2 KB 的消息算作多个大小为 2 KB 的消息。 每当每个中心出现 100 个消息时，Azure 门户中的消息计数图表就会更新。

例如，假设你有一个应用程序服务器和三个客户端：

应用服务器将 1 KB 消息广播到所有连接的客户端，从应用服务器到服务的消息被视为免费入站消息。 只有三个从服务发送到每个客户端的消息才能作为出站消息计费。

客户端 A 将 1 KB 的消息发送给另一个客户端 B，无需通过应用服务器。 来自客户端 A 到服务的消息是免费的入站消息。 从服务到客户端 B 的消息按出站消息计费。

如果有三个客户端和一个应用程序服务器。 1 个客户端发送 1 条 4 KB 消息，让服务器广播到所有客户端。 计费消息计数为八：从服务到应用程序服务器的一条消息，以及从服务到客户端的三条消息。 每条消息算作 2 条 2-KB 大小的消息。

## <a name="how-connections-are-counted"></a>如何统计连接数

Azure SignalR 服务有服务器连接和客户端连接。 默认情况下，每个应用程序服务器启动时每个中心有五个初始连接，每个客户端有一个客户端连接。

Azure 门户中显示的连接计数包括服务器连接和客户端连接。

例如，假设您有两个应用程序服务器，并且您在代码中定义了五个中心。 服务器连接计数将为50：2个应用服务器 * 5 个中心 * 5 个连接。

ASP.NET SignalR 在计算服务器连接数方面有所不同。 除了定义的中心以外，它还包括一个默认的中心。 默认情况下，每个应用程序服务器需要 5 个额外的初始服务器连接。 默认中心的初始连接计数与其他中心保持一致。

服务和应用程序服务器保持同步连接状态并对服务器连接进行调整，以获得更好的性能和服务稳定性。  因此，你可能会看到服务器连接数不时发生变化。

## <a name="how-inboundoutbound-traffic-is-counted"></a>如何统计入站/出站流量

发送到服务的消息是入站消息。 从服务发送的消息是出站消息。 流量以字节为单位计算。

## <a name="related-resources"></a>相关资源

- [Azure Monitor 中的聚合类型](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR 配置](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
