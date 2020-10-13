---
title: Azure SignalR 服务中的服务模式
description: 有关 Azure SignalR 服务中各种服务模式的概述，说明了各模式之间的差异及适用的用户场景
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 2cdce64b0ff03521a5848e2b4fd6e01431b5cc16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89514793"
---
# <a name="service-mode-in-azure-signalr-service"></a>Azure SignalR 服务中的服务模式

服务模式是 Azure SignalR 服务中的一个重要概念。 新建 SignalR 资源时，你需要指定服务模式：

:::image type="content" source="media/concept-service-mode/create.png" alt-text="创建时选择服务模式":::

稍后还可以在设置菜单中对其进行更改：

:::image type="content" source="media/concept-service-mode/update.png" alt-text="创建时选择服务模式":::

Azure SignalR 服务目前支持三种服务模式：默认、无服务器和经典  。 SignalR 资源在不同模式下的行为方式有所不同。 本文将介绍它们之间的差异，以及如何根据场景选择合适的服务模式。

## <a name="default-mode"></a>默认模式

默认模式是新建 SignalR 资源时服务模式的默认值。 在这种模式下，你的应用程序像典型的 ASP.NET Core（或 ASP.NET）SignalR 应用程序一样工作；在该应用程序中，你有一个托管集线器的 Web 服务器（以下称为集线器服务器），并且客户端可以与集线器服务器进行双工实时通信。 唯一的区别是，客户端和服务器都连接到 SignalR 服务并将该服务用作代理，而不是直接连接客户端和服务器。 下图说明了默认模式下的典型应用程序结构：

:::image type="content" source="media/concept-service-mode/default.png" alt-text="创建时选择服务模式":::

因此，如果你拥有 SignalR 应用程序并且希望与 SignalR 服务集成，则大多数情况下，默认模式应是正确选择。

### <a name="connection-routing-in-default-mode"></a>默认模式下的连接路由

默认模式下，集线器服务器和 SignalR 服务之间将存在 WebSocket 连接（称为服务器连接）。 这些连接用于在服务器和客户端之间传输消息。 当连接了一个新的客户端时，SignalR 服务会通过现有服务器连接将客户端路由到一台集线器服务器（假设你拥有多台服务器）。 然后，客户端连接将在其生存期内保留在同一集线器服务器上。 客户端发送消息时，消息将始终转到同一集线器服务器。 通过这一行为，你可以安全地维护集线器服务器上各个连接的某些状态。 例如，如果你要在服务器和客户端之间流式传输某些内容，则无需考虑数据包转到不同服务器这一情况。

> [!IMPORTANT]
> 这也意味着在默认模式下，必须先连接服务器，客户端才能建立连接。 如果由于网络中断或服务器重启而断开了所有集线器服务器的连接，则客户端连接将收到一条错误消息，告知你未连接任何服务器。 因此，你有责任确保在任何时候至少有一台集线器服务器连接到 SignalR 服务（例如，你拥有多台集线器服务器，需确保它们不会因维护等情况而同时脱机）。

此路由模型还意味着当集线器服务器脱机时，路由到该服务器的连接将被中断。 因此，在集线器服务器因维护而脱机时，你应该预料到连接会中断，并正确处理重新连接，以免对应用程序造成负面影响。

## <a name="serverless-mode"></a>无服务器模式

顾名思义，无服务器模式是一种你不能有任何集线器服务器的模式。 与默认模式相比，此模式下的客户端不需要集线器服务器即可进行连接。 所有连接都连接到无服务器模式下的服务，并且服务负责维护客户端连接，例如处理客户端 ping（默认模式下，这由集线器服务器处理）。

在此模式下也没有服务器连接（如果尝试使用服务 SDK 建立服务器连接，则会收到错误）。 因此，也不会有连接路由和服务器-客户端粘性（如默认模式一节中所述）。 但你仍可以使用服务器端应用程序将消息推送到客户端。 可以通过两种方式实现此目标：使用 [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) 进行一次性发送，或通过 WebSocket 连接，这样便可更高效地发送多条消息（请注意，此 WebSocket 连接不同于服务器连接）。

> [!NOTE]
> SignalR 服务[管理 SDK](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md) 支持 REST API 和 WebSocket 方式。 如果使用的是 .NET 以外的语言，则还可以按照此[规范](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)手动调用 REST API。
>
> 如果使用的是 Azure Functions，则可以使用 [Azure Functions 的 SignalR 服务绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service)（以下称为函数绑定），将消息作为输出绑定发送。

服务器应用程序还可以从客户端接收消息和连接事件。 服务会使用 Webhook 将消息和连接事件传递到预配置的终结点（称为上游）。 相较于默认模式，无法保证粘性，且 HTTP 请求可能比 WebSocket 连接低效。

有关如何配置上游的详细信息，请参阅此[文档](https://docs.microsoft.com/azure/azure-signalr/concept-upstream)。

下图说明了无服务器模式的工作方式：

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="创建时选择服务模式":::

> [!NOTE]
> 请注意，在默认模式下，如果你不想通过集线器服务器，还可以使用 REST API/管理 SDK/函数绑定将消息直接发送到客户端。 但在默认模式下，客户端连接仍由集线器服务器处理，且上游在该模式下无法正常工作。

## <a name="classic-mode"></a>经典模式

经典模式是默认模式和无服务器模式的混合模式。 在此模式下，连接模式取决于建立客户端连接时是否连接了集线器服务器。 如果有集线器服务器，客户端连接将路由到一台集线器服务器。 否则，它将进入无服务器模式，在此模式下，客户端到服务器的消息无法传递到集线器服务器。 这将导致一些差异；例如，如果所有集线器服务器在短时间内都不可用，则在此期间创建的所有客户端连接将处于无服务器模式，并且无法将消息发送到集线器服务器。

> [!NOTE]
> 经典模式主要是为了实现那些在默认模式和无服务器模式尚不存在时创建的应用程序的后向兼容性。 强烈建议不再使用此模式。 对于新应用程序，请根据场景选择默认或无服务器模式。 对于现有应用程序，还建议查看用例并选择适当的服务模式。

经典模式也不支持某些新增功能，例如无服务器模式下的上游。

## <a name="choose-the-right-service-mode"></a>选择正确的服务模式

现在，你应了解了服务模式之间的差异，并知道了如何在它们之间进行选择。 正如上一节中所述，不推荐使用经典模式，而你应仅在默认模式或无服务器模式之间进行选择。 以下是一些其他提示，可帮助你为新应用程序选择正确的模式，并停用现有应用程序的经典模式。

* 如果熟悉 SignalR 库的工作方式，且希望从自托管 SignalR 转为使用 Azure SignalR 服务，请选择默认模式。 默认模式的工作方式与自托管 SignalR 完全相同（你可以使用 SignalR 库中的同一编程模型），SignalR 服务仅充当客户端和集线器服务器之间的代理。

* 如果要新建应用程序，且不想维护集线器服务器和服务器连接，请选择无服务器模式。 此模式通常与 Azure Functions 一起使用，这样你就根本无需维护任何服务器。 你仍可以进行双工通信（使用 REST API/管理 SDK/函数绑定 + 上游），但编程模型将不同于 SignalR 库。

* 如果你同时拥有服务于客户端连接的集线器服务器和直接向客户端推送消息的后端应用程序（例如通过 REST API），则你仍应选择默认模式。 请记住，默认模式和无服务器模式之间的主要区别在于你是否拥有集线器服务器以及客户端接连如何进行路由。 两种模式下均可使用 REST API/管理 SDK/函数绑定。

* 如果你确实处于一种混合场景（例如，对于同一 SignalR 资源，你有两个不同的集线器，其中一个用作传统的 SignalR 集线器，另一个与 Azure Functions 结合使用并且没有集线器服务器）下，则你真的应考虑将其分为两个 SignalR 资源，一个处于默认模式，另一个处于无服务器模式。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用默认模式和无服务器模式，请阅读以下文章：

* [Azure SignalR 服务内部](signalr-concept-internals.md)

* [通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](signalr-concept-serverless-development-config.md)
