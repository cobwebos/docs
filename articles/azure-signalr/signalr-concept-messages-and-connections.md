---
title: Azure SignalR 服务中的消息和连接
description: 有关 Azure SignalR 服务中的消息和连接的重要概念概述。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 5f6428231a3639738e8fb52e7dc3f2f2a3d2a26e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392818"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Azure SignalR 服务中的消息和连接

Azure SignalR 服务的计费模型基于连接数和消息数。 本文介绍消息和连接数目的定义方式，以及它们如何影响计费。


## <a name="message-formats"></a>消息格式 

Azure SignalR 服务支持 ASP.NET Core SignalR： [JSON](https://www.json.org/)和[MessagePack](/aspnet/core/signalr/messagepackhubprotocol)相同的格式。

## <a name="message-size"></a>消息大小

Azure SignalR 服务对消息大小不施加限制。

大消息将拆分为小于 2 KB 的若干较小消息，并单独传输。 消息拆分与组合由 SDK 进行处理， 无需开发人员的干预。

大消息确实会对消息传送性能造成负面影响。 请尽量使用小消息，并通过测试来为每种使用方案确定最佳消息大小。

## <a name="how-messages-are-counted-for-billing"></a>消息如何影响计费

计费时，我们只会统计来自 Azure SignalR 服务的出站消息。 将忽略客户端与服务器之间的 Ping 消息。

大于 2 KB 的消息算作多个大小为 2 KB 的消息。 每当每个中心出现 100 个消息时，Azure 门户中的消息计数图表就会更新。

例如，假设你有 3 个客户端和 1 个应用程序服务器。 1 个客户端发送 1 条 4 KB 消息，让服务器广播到所有客户端。 消息计数是 8：服务向应用程序服务器发送 1 条消息，服务向客户端发送 3 条消息。 每条消息算作 2 条 2-KB 大小的消息。

## <a name="how-connections-are-counted"></a>如何统计连接数

存在服务器连接和 Azure SignalR 服务的客户端连接。 默认情况下，每个应用程序服务器都开始每个集线器5个初始连接，每个客户端都有一个客户端连接。

Azure 门户中显示的连接计数包括服务器连接和客户端连接。

例如，假设你有 2 个应用程序服务器，并在代码中定义了 5 个中心。 服务器连接计数将为50：2个应用服务器 * 5 个中心 * 5 个连接。

ASP.NET SignalR 在计算服务器连接数方面有所不同。 除了定义的中心以外，它还包括一个默认的中心。 默认情况下，每个应用程序服务器需要5个以上的初始服务器连接。 默认中心的初始连接计数与其他中心的初始连接计数保持一致。

在应用程序服务器的生存期内，服务和应用程序服务器保持同步连接状态，并对服务器连接进行调整，以获得更好的性能和服务稳定性。 因此，您可能会发现服务器连接号码会随时间变化而变化。

## <a name="how-inboundoutbound-traffic-is-counted"></a>如何统计入站/出站流量

入站流量与出站流量之间的差别是从 Azure SignalR 服务的角度考虑的。 流量以字节为单位计算。

## <a name="related-resources"></a>相关资源

- [Azure Monitor 中的聚合类型](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR 配置](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
