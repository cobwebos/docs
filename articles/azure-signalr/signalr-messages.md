---
title: Azure SignalR 中的消息和连接
description: 有关 Azure SignalR 服务中的消息和连接的重要概念概述。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 5a0430e9ad124319147342c49fc51e11472ac8ff
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812650"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Azure SignalR 服务中的消息和连接

Azure SignalR 服务采用基于连接数和消息数的计费模型。 下面将会解释消息和连接的定义，以及它们如何影响计费。

## <a name="message-formats-supported"></a>支持的消息格式

Azure SignalR 服务支持的格式与 ASP.NET Core SignalR 相同：[JSON](https://www.json.org/) 和 [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>消息大小

Azure SignalR 服务对消息大小不施加限制。

在实践中，大消息将拆分为小于 2 KB 的若干较小消息，并单独传输这些小消息。 消息拆分与组合由 SDK 处理， 无需开发人员的干预。

但是，大消息对消息传送性能有负面影响。 请尽量使用小消息，并通过测试来为每种使用方案选择最佳消息大小。

## <a name="how-to-count-messages-for-billing-purpose"></a>消息如何影响计费？

我们只会统计来自 SignalR 服务的出站消息，将忽略客户端与服务器之间的 ping 消息。

大于 2 KB 的消息算作多个大小为 2 KB 的消息。 每当每个中心出现 100 个消息时，Azure 门户中的消息计数图表就会更新。

例如，某个用户有 3 个客户端和 1 个应用程序服务器。 1 个客户端发送 1 条 4 KB 消息，让服务器广播到所有客户端。 消息计数将是 8：服务向应用程序服务器发送 1 条消息，服务向客户端发送 3 条消息，每条消息算作 2-KB 大小的 2 条消息。

在消息数累积超过 100 之前，Azure 门户中显示的消息计数会一直保持为 0。

## <a name="how-to-count-connections"></a>如何统计连接数？

有服务器连接和客户端连接。 默认情况下，每个中心的每个应用程序服务器与 SignalR 服务建立 5 个连接，每个客户端与 SignalR 服务建立 1 个客户端连接。

Azure 门户中显示的连接计数包括服务器连接和客户端连接。

例如，某个用户有 2 个应用程序服务器，并在代码中定义了 5 个中心。 Azure 门户中显示的服务器连接计数将是 2 个应用服务器 * 5 个中心 * 5 个连接/中心 = 50 个服务器连接。

## <a name="related-resources"></a>相关资源

- [ASP.NET Core SignalR 配置](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)