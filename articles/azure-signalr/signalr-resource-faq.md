---
title: Azure SignalR 服务常见问题解答
description: 获取有关 Azure SignalR 服务的常见问题的解答，包括故障排除和典型使用方案。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 5d6b46e288007bc0bbac53a97b1bdd5e727b8ac8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91405116"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR 服务常见问题解答

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR 服务是否随时可用于生产？

是的，对 [ASP.NET Core SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr) 和 [ASP.NET SignalR](https://docs.microsoft.com/aspnet/signalr/overview/getting-started/introduction-to-signalr) 的支持都已正式发布。

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>如果存在多个应用程序服务器，客户端消息是发送到所有服务器，还是只发送到其中的一个服务器？

客户端与应用程序服务器之间存在一对一映射。 来自一个客户端的消息始终会发送到同一个应用程序服务器。

在客户端或应用程序服务器断开连接之前，将保持映射。

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>如果我的某个应用程序服务器已关闭，如何发现这种情况并收到通知？

Azure SignalR 服务将监视应用程序服务器的检测信号。
如果在指定的一段时间内未收到检测信号，则认为应用程序服务器处于脱机状态。 映射到此应用程序服务器的所有客户端连接将会断开。

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>从 ASP.NET Core SignalR SDK 切换到 Azure SignalR 服务 SDK 时，自定义 `IUserIdProvider` 为何引发异常？

调用 `IUserIdProvider` 时，ASP.NET Core SignalR SDK 与 Azure SignalR 服务 SDK 的参数 `HubConnectionContext context` 不相同。

在 ASP.NET Core SignalR 中，`HubConnectionContext context` 是包含所有属性有效值的物理客户端连接的上下文。

在 Azure SignalR 服务 SDK 中，`HubConnectionContext context` 是逻辑客户端连接的上下文。 物理客户端将连接到 Azure SignalR 服务实例，因此，只提供有限数量的属性。

目前只能访问 `HubConnectionContext.GetHttpContext()` 和 `HubConnectionContext.User`。
你可以[查看源代码](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)。

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>是否可以使用 ASP.NET Core SignalR 配置在服务器端的 Azure SignalR 服务中可用的传输？ 例如，是否可以禁用 WebSocket 传输？

不是。

默认情况下，Azure SignalR 服务提供 ASP.NET Core SignalR 所支持的所有三种传输。 无法对其进行配置。 Azure SignalR 服务将处理连接以及所有客户端连接的传输。

可以按照 [ASP.NET Core SignalR 配置](https://docs.microsoft.com/aspnet/core/signalr/configuration#configure-allowed-transports-1)中所述配置客户端传输。

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Azure 门户中显示的指标（例如消息计数或连接计数）的含义是什么？ 我应该选择哪种聚合类型？

可以在 [Azure SignalR 服务中的消息和连接](signalr-concept-messages-and-connections.md)中找到有关计算这些指标的详细信息。

在 Azure SignalR 服务资源的概述窗格上，我们已经为你选择了合适的聚合类型。 如果转到指标窗格，你可以将该聚合类型作为引用应用到 [Azure SignalR 服务中的消息和连接](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr)。

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>`Default`、`Serverless` 和 `Classic` 服务模式的含义是什么？ 如何选择？

对于新应用程序，应只使用默认和无服务器模式。 主要区别在于你是否拥有与服务建立服务器连接的应用程序服务器（例如，使用 `AddAzureSignalR()` 连接到服务）。 如果是，则使用默认模式，否则使用无服务器模式。

经典模式旨在实现现有应用程序的后向兼容性，因此不应将其用于新应用程序。

有关服务模式的详细信息，请参阅[这篇文档](concept-service-mode.md)。

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>能否在无服务器模式下从客户端发送消息？

如果在 SignalR 实例中配置 Upstream，则可以从客户端发送消息。 Upstream 是一组终结点，可接收来自 SignalR 服务的消息和连接事件。 如果未配置 Upstream，则将忽略来自客户端的消息。

有关 Upstream 的详细信息，请参阅[这篇文档](concept-upstream.md)。

Upstream 目前以公共预览版提供。

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>将 Azure SignalR 服务与 ASP.NET SignalR 结合使用时是否有功能差异？

使用 Azure SignalR 服务时，ASP.NET SignalR 的某些 API 和功能不受支持：
- 不支持在客户端和中心之间传递任意状态的功能（通常称为 `HubState`）。
- 不支持 `PersistentConnection` 类。
- 不支持永久帧传输。
- Azure SignalR 服务不再重播客户端脱机时发送给客户端的消息。
- 使用 Azure SignalR 服务时，在连接期间内，一个客户端连接的流量始终路由（也称为“粘滞”）到一个应用服务器实例。

对 ASP.NET SignalR 的支持侧重于兼容性，因此并非 ASP.NET Core SignalR 的所有新功能都受到支持。 例如，MessagePack 和流式处理仅适用于 ASP.NET Core SignalR 应用程序 。

你可以为不同的服务模式（`Classic`、`Default` 和 `Serverless`）配置 Azure SignalR 服务。 ASP.NET 不支持 `Serverless` 模式。 也不支持数据平面 REST API。

## <a name="where-does-my-data-reside"></a>我的数据存放在什么位置？

Azure SignalR 服务作为数据处理器服务工作。 它不会存储任何客户内容，且设计包含数据驻留。 如果将 Azure SignalR 服务与其他 Azure 服务（例如 Azure 存储）结合用于诊断，请参阅[此白皮书](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)，以获取有关如何将数据驻留在 Azure 区域中的指南。
