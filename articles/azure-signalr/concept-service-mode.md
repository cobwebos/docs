---
title: Azure SignalR 服务中的服务模式
description: Azure SignalR 服务中的不同服务模式概述，说明它们的区别和适用的用户方案
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 2cdce64b0ff03521a5848e2b4fd6e01431b5cc16
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514793"
---
# <a name="service-mode-in-azure-signalr-service"></a>Azure SignalR 服务中的服务模式

服务模式是 Azure SignalR 服务中的一个重要概念。 创建新的 SignalR 资源时，系统会要求你指定服务模式：

:::image type="content" source="media/concept-service-mode/create.png" alt-text="创建时选择服务模式":::

你还可以稍后在 "设置" 菜单中更改它：

:::image type="content" source="media/concept-service-mode/update.png" alt-text="更新服务模式":::

Azure SignalR 服务目前支持三种服务模式： **默认**、 **无服务器** 和 **经典**。 SignalR 资源在不同模式下的行为方式有所不同。 在本文中，你将了解它们之间的差异，以及如何根据你的方案选择合适的服务模式。

## <a name="default-mode"></a>默认模式

默认模式是在创建新的 SignalR 资源时服务模式的默认值。 在此模式下，应用程序可用作典型的 ASP.NET Core (或 ASP.NET) SignalR 应用程序，在该应用程序中，你的 web 服务器承载名为 hub server 下文) 的 (集线器 唯一的区别是，客户端和服务器都不直接连接客户端和服务器，而是连接到 SignalR 服务并将该服务用作代理。 下图显示了默认模式下的典型应用程序结构：

:::image type="content" source="media/concept-service-mode/default.png" alt-text="默认模式下的应用程序结构":::

因此，如果你有一个 SignalR 的应用程序并想要与 SignalR 服务集成，则在大多数情况下，默认模式应是正确的选择。

### <a name="connection-routing-in-default-mode"></a>默认模式下的连接路由

在默认模式下，中心服务器与 SignalR 服务之间将存在 websocket 连接 (称为服务器连接) 。 这些连接用于在服务器和客户端之间传输消息。 当连接了新的客户端时，SignalR service 会将客户端路由到一个中心服务器 (假设你有多个服务器) 通过现有服务器连接。 然后，客户端连接将在其生存期内保持同一个中心服务器。 当客户端发送消息时，它们始终会转向同一中心服务器。 利用此行为，你可以安全地维护中心服务器上单个连接的某些状态。 例如，如果想要在服务器和客户端之间进行流式处理，则不需要考虑数据包发送到不同服务器的情况。

> [!IMPORTANT]
> 这也意味着在默认模式下，客户端在未首先连接服务器的情况下无法连接。 如果所有中心服务器由于网络中断或服务器重新启动而断开连接，则客户端连接会出现错误，指示未连接服务器。 因此，你有责任确保至少有一台中枢服务器连接到 SignalR 服务 (例如，有多台集线器服务器，并确保它们不会同时为维护) 之类的时间脱机。

此路由模型还意味着当中心服务器脱机时，路由该服务器的连接将被丢弃。 因此，当中心服务器脱机进行维护并正确地处理重新连接时，应会出现连接断开，以便不会对应用程序产生负面影响。

## <a name="serverless-mode"></a>无服务器模式

无服务器模式顾名思义，是一种不能具有任何中心服务器的模式。 与默认模式相比，在此模式下，客户端不需要集线器服务器即可进行连接。 所有连接都连接到 "无服务器" 模式下的服务，服务负责维护客户端连接，例如处理客户端 ping (默认模式下，此操作由中心服务器) 处理。

此外，在此模式下没有服务器连接 (如果你尝试使用服务 SDK 建立服务器连接，则) 会出现错误。 因此，还没有连接路由和服务器客户端粘性 (，如默认模式部分) 所述。 但你仍可以使用服务器端应用程序将消息推送到客户端。 这可以通过两种方式完成：使用 [REST api](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) 进行一次性发送，或通过 websocket 连接，以便更有效地发送多条消息 (注意，此 websocket 连接与服务器连接) 不同。

> [!NOTE]
> SignalR service [MANAGEMENT SDK](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md)支持 REST API 和 websocket 方式。 如果使用的是 .NET 以外的语言，则还可以按照此 [规范](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)手动调用 REST api。
>
> 如果使用 Azure Functions，则可以将 [SignalR 服务绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service) 用于称为函数绑定) 的 Azure Functions (下文，以作为输出绑定发送消息。

服务器应用程序也可以从客户端接收消息和连接事件。 服务将使用 webhook 将消息和连接事件传递到)  (的预配置终结点。 与默认模式相比，不保证粘性和 HTTP 请求的效率可能低于 websocket 连接。

有关如何配置上游的详细信息，请参阅此 [文档](https://docs.microsoft.com/azure/azure-signalr/concept-upstream)。

下面的关系图演示无服务器模式的工作方式：

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="无服务器模式下的应用程序结构":::

> [!NOTE]
> 请注意，在默认模式下，你还可以使用 REST API/management SDK/函数绑定直接将消息发送到客户端（如果你不想通过中心服务器）。 但在默认模式下，客户端连接仍由中心服务器处理，而上游在该模式下将无法工作。

## <a name="classic-mode"></a>经典模式

经典模式为默认模式和无服务器模式。 在此模式下，连接模式取决于建立客户端连接时是否连接了中心服务器。 如果有集线器服务器，客户端连接将路由到中心服务器。 否则，将进入 "无服务器" 模式，在该模式下，客户端到服务器的消息无法传递到中心服务器。 这会导致一些差异，例如，如果所有中心服务器暂时不可用，则在此期间创建的所有客户端连接都将处于无服务器模式，并且无法将消息发送到中心服务器。

> [!NOTE]
> 经典模式主要用于在存在默认和无服务器模式之前创建的那些应用程序的向后兼容性。 强烈建议不要再使用此模式。 对于新应用程序，请根据你的方案选择 "默认" 或 "无服务器"。 对于现有应用程序，还建议查看用例，并选择适当的服务模式。

经典模式还不支持无服务器模式下的某些新功能，例如上游。

## <a name="choose-the-right-service-mode"></a>选择适当的服务模式

现在，您应该了解服务模式之间的差异，以及如何在它们之间进行选择。 正如你在上一节中所学到的那样，不鼓励经典模式，并且你只应选择 "默认" 和 "无服务器"。 下面是一些提示，可帮助你为新应用程序选择合适的选项，并为现有应用程序停用经典模式。

* 如果已熟悉 SignalR 库的工作原理，并想要从自承载 SignalR 移动到使用 Azure SignalR 服务，请选择 "默认模式"。 默认模式的工作方式与自承载的 SignalR (完全相同，您可以使用 SignalR 库中的相同编程模型) ，SignalR 服务只充当客户端和中心服务器之间的代理。

* 如果要创建新的应用程序，而不想维护中心服务器和服务器连接，请选择无服务器模式。 此模式通常与 Azure Functions 一起使用，因此无需维护任何服务器。 你仍可以使用 REST API/管理 SDK/函数绑定 + 上游) 进行双工通信 (但编程模型将不同于 SignalR 库。

* 如果你有两台中心服务器来提供客户端连接和后端应用程序，以直接将消息推送到客户端 (例如通过 REST API) ，则仍应选择 "默认" 模式。 请记住，默认模式和无服务器模式之间的主要区别在于是否有集线器服务器以及如何路由客户端连接。 可以在这两种模式下使用 REST API/management SDK/函数绑定。

* 例如，如果你确实有混合方案，则在同一 SignalR 资源上有两个不同的集线器，一个用于传统的 SignalR 中心，另一个用于 Azure Functions，而不是使用中心服务器，则你应该真正考虑将它们分为两个 SignalR 资源，一个处于默认模式，一个处于无服务器模式。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用默认模式和无服务器模式，请阅读以下文章：

* [Azure SignalR 服务内部](signalr-concept-internals.md)

* [通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](signalr-concept-serverless-development-config.md)
