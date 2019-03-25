---
title: 什么是 Azure SignalR 服务？
description: Azure SignalR 服务概述。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 198eb0ff6c9f8de311cc2d39ba8fb7c8b6ed3a11
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552073"
---
# <a name="what-is-azure-signalr-service"></a>什么是 Azure SignalR 服务？

Azure SignalR Service 简化了通过 HTTP 向应用程序添加实时 Web 功能的过程。 这种实时功能允许服务将内容更新推送到连接的客户端，例如单页 Web 或移动应用程序。 因此，无需轮询服务器，或提交针对更新的新 HTTP 请求，即可更新客户端。

本文简要介绍了 Azure SignalR Service。

## <a name="what-is-azure-signalr-service-used-for"></a>Azure SignalR Service 的用途是什么？

存在多种需要实时内容更新的应用程序类型。 下面的示例适合使用 Azure SignalR Service：

* 需要从服务器进行高频率更新的应用。 示例包括游戏、投票、拍卖、地图和 GPS 应用。
* 仪表板和监视应用。 示例包括公司仪表板和即时销售更新。
* 协作应用。 协作应用的示例包括白板应用和团队会议软件。
* 需要通知的应用。 社交网络、电子邮件、聊天、游戏、旅行警报和很多其他应用都需使用通知。

SignalR 提供用于生成实时 Web 应用程序的多种技术的抽象概念。 [Websocket](https://wikipedia.org/wiki/WebSocket) 是最佳传输，但在其他选项不可用时，则使用[服务器发送事件 (SSE)](https://wikipedia.org/wiki/Server-sent_events) 和长轮询等其他技术。 基于服务器和客户端上受支持的功能，SignalR 自动检测并初始化合适的传输。

此外，SignalR 还为实时应用程序提供编程模型，该模型允许服务器将消息发送到所有连接或属于特定用户或已放置在任意组中的连接子集。

## <a name="how-to-use-azure-signalr-service"></a>如何使用 Azure SignalR Service

目前有三种可使用 Azure SignalR Service 的方法：

- [扩展 ASP.NET Core SignalR 应用](signalr-concept-scale-aspnet-core.md) - 将 Azure SignalR Service 与ASP.NET Core SignalR 应用程序集成，以扩展到成千上万的连接。
- [生成无服务器的实时应用](signalr-concept-azure-functions.md) - 使用 Azure Functions 与 Azure SignalR Service 的集成，以 JavaScript、C# 和 Java 等语言生成无服务器的实时应用。
- [通过 REST API 将消息从服务器发送到客户端](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) - Azure SignalR Service 提供的 REST API 使应用程序能够以任何支持 REST 的编程语言向使用 SignalR Service 连接的客户端发送消息。