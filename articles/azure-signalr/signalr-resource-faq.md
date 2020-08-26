---
title: Azure SignalR 服务常见问题解答
description: 可以快速访问有关 Azure SignalR 服务的常见问题解答，这些问题涉及故障排除和典型使用方案。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: c944ae3a5d647cc457edd20a5d3dd0489e19e286
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192279"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR 服务常见问题解答

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR 服务是否随时可用于生产？

是的。
有关正式版的通告，请参阅 [Azure SignalR 服务现已推出正式版](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/)。 

完全支持 [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction)。

对 ASP.NET SignalR 的支持目前仍以公共预览版提供。  此处提供了[代码示例](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)。

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>客户端连接关闭并显示错误消息“没有可用的服务器”。 它意味着什么？

仅当客户端正在向 SignalR 服务发送消息时，才会发生此错误。

如果你没有任何应用程序服务器并且只使用 SignalR 服务 REST API，则此行为是**设计使然**。
在无服务器体系结构中，客户端连接处于“侦听”模式，不会向 SignalR 服务发送任何消息。 
详细了解 [REST API](./signalr-quickstart-rest-api.md)。

如果你有应用程序服务器，则此错误消息意味着没有任何应用程序服务器连接到 SignalR 服务实例。

可能的原因包括：
- 没有任何应用程序服务器连接到 SignalR 服务。 检查应用程序服务器日志，以查看可能的连接错误。 在使用多个应用程序服务器的高可用性设置中，这种情况非常罕见。
- SignalR 服务实例存在连接问题。 此问题是暂时性的，可自动恢复。
如果此问题持续一个小时以上，请[在 GitHub 上提出问题](https://github.com/Azure/azure-signalr/issues/new)，或者[在 Azure 中创建支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>如果存在多个应用程序服务器，客户端消息是发送到所有服务器，还是只发送到其中的一个服务器？

客户端与应用程序服务器之间存在一对一映射。 来自一个客户端的消息始终会发送到同一个应用程序服务器。

在客户端或应用程序服务器断开连接之前，会一直保持客户端与应用程序服务器之间的映射。

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>如果我的某个应用程序服务器已关闭，如何发现这种情况并收到通知？

SignalR 服务将监视应用程序服务器的检测信号。
如果在指定的一段时间内未收到检测信号，则认为应用程序服务器处于脱机状态。 映射到此应用程序服务器的所有客户端连接将会断开。

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>从 ASP.NET Core SignalR SDK 切换到 Azure SignalR 服务 SDK 时，自定义 `IUserIdProvider` 为何引发异常？

调用 `IUserIdProvider` 时，ASP.NET Core SignalR SDK 与 Azure SignalR 服务 SDK 的 `HubConnectionContext context` 参数不相同。

在 ASP.NET Core SignalR 中，`HubConnectionContext context` 是包含所有属性有效值的物理客户端连接的上下文。

在 Azure SignalR 服务 SDK 中，`HubConnectionContext context` 是逻辑客户端连接的上下文。 物理客户端连接将连接到 SignalR 服务实例，因此，只提供有限数量的属性。

目前只能访问 `HubConnectionContext.GetHttpContext()` 和 `HubConnectionContext.User`。
可在[此处](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)查看源代码。

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>是否可以像在服务器端使用 ASP.NET Core SignalR 配置传输一样，在 SignalR 服务中配置传输？ 例如，禁用 WebSocket 传输？

不是。

默认情况下，Azure SignalR 服务提供 ASP.NET Core SignalR 所支持的所有三种传输。 这些传输不可配置。 SignalR 服务将处理连接以及所有客户端连接的传输。

可根据[此文](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)中所述配置客户端传输。

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Azure 门户中显示的指标（例如消息计数或连接计数）的含义是什么？ 我应该选择哪种聚合类型？

可以在[此处](signalr-concept-messages-and-connections.md)找到有关如何计算这些指标的详细信息。

在 Azure SignalR 服务资源的“概述”边栏选项卡中，我们已经为你选择了合适的聚合类型。 如果转到“指标”边栏选项卡，则可以将[此处](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr)的聚合类型用作参考。

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>服务模式 `Default`/`Serverless`/`Classic` 的含义是什么？ 如何选择？

模式：
* `Default` 模式需要中心服务器。 当没有可用于中心的服务器连接时，客户端尝试连接到该中心将失败。
* `Serverless` 模式不允许任何服务器连接，即它将拒绝所有服务器连接，所有客户端必须处于无服务器模式。
* `Classic` 模式为混合状态。 当中心具有服务器连接时，新客户端将被路由到中心服务器；否则，客户端将进入无服务器模式。

  这可能会导致某些问题，例如，所有服务器连接暂时丢失，某些客户端将进入无服务器模式，而不是路由到中心服务器。

选择方案：
1. 如果没有中心服务器，选择 `Serverless`。
1. 如果所有中心都有中心服务器，选择 `Default`。
1. 如果一些中心有中心服务器，而其他中心没有，请选择 `Classic`，但这可能会导致某些问题，更好的方法是创建两个实例：一个实例为 `Serverless`，另一个实例为 `Default`。

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>使用适用于 ASP.NET SignalR 的 Azure SignalR 时是否有功能差异？
使用 Azure SignalR 时，ASP.NET SignalR 的某些 API 和功能不再受到支持：
- 使用 Azure SignalR 时，不支持在客户端和中心（通常称为 `HubState`）之间传递任意状态的功能
- 使用 Azure SignalR 时，尚不支持 `PersistentConnection` 类
- 使用 Azure SignalR 时，不支持永久帧传输
- 客户端脱机时，Azure SignalR 不再重播发送给客户端的消息
- 使用 Azure SignalR 时，在连接期间，始终会将一个客户端连接的流量路由（也称为 粘滞）到一个应用服务器实例

对 ASP.NET SignalR 的支持侧重于兼容性，因此并非 ASP.NET Core SignalR 的所有新功能都受到支持。 例如，MessagePack 和流式处理等仅适用于 ASP.NET Core SignalR 应用程序 。

可以将 SignalR 服务配置为不同的服务模式：`Classic`/`Default`/`Serverles`s。 在此 ASP.NET 支持中，不支持 `Serverless` 模式。 也不支持数据平面 REST API。

## <a name="where-do-my-data-reside"></a>我的数据存放在什么位置？

Azure SignalR 服务以数据处理器服务的形式运行。 该服务不会存储任何客户内容，并且设计会保证数据驻留。 如果将 Azure SignalR 服务与其他 Azure 服务（例如用于诊断的 Azure 存储）一起使用，请参阅[此处](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)，以获取有关如何将数据驻留在 Azure 区域中的指南。
