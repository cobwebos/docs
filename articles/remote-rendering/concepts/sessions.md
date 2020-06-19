---
title: 远程渲染会话
description: 描述远程渲染会话的定义
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 509375459d019ead5a7992b808044a75e2666393
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758854"
---
# <a name="remote-rendering-sessions"></a>远程渲染会话

在 Azure 远程渲染 (ARR) 中，会话是一个关键概念。 本文解释了到底什么是会话。

## <a name="overview"></a>概述

Azure 远程渲染的工作原理是，将复杂的渲染任务卸载到云中。 这些渲染任务无法通过任何服务器来完成，因为大多数云服务器没有 GPU。 由于涉及的数据量，以及以交互式帧速率生成结果的硬性要求，哪个服务器处理哪些用户请求的职责不能像较常见的 Web 流量一样，直接移交给另一台计算机。

这意味着，在使用 Azure 远程渲染时，必须以独占方式保留具有必要硬件功能的云服务器来处理渲染请求。 “会话”是指与此服务器交互相关的所有内容。 它从保留（租用）计算机供你使用的初始请求开始，然后是执行加载和操作模型所要执行的所有命令，最后是在云服务器上解除租约。

## <a name="managing-sessions"></a>管理会话

可以通过多种方式来管理会话以及与会话交互。 与语言无关的创建、更新和关闭会话的方法是通过[会话管理 REST API](../how-tos/session-rest-api.md)。 在 C# 和 C++ 中，这些操作通过 `AzureFrontend` 和 `AzureSession` 类来公开。 对于 Unity 应用程序，`ARRServiceUnity` 组件提供了更多实用工具函数。

连接到活动会话后，[加载模型](models.md)以及与场景交互的操作将通过 `AzureSession` 类来公开。

### <a name="managing-multiple-sessions-simultaneously"></a>同时管理多个会话

无法从一个设备完全连接到多个会话。 不过，可以从单个应用程序创建、观察和关闭任意数量的会话。 只要该应用不是要连接到某个会话，就不需要在诸如 HoloLens 2 的设备上运行。 如果希望通过中心机制来控制会话，则可使用此类实现的用例。 例如，可构建一个 Web 应用，多个平板电脑和 HoloLenses 都可以登录该应用。 然后，该应用可以在平板电脑上显示选项，例如要显示的 CAD 模型。 如果用户进行了选择，则会将此信息传达给所有 HoloLenses 以创造共享体验。

## <a name="session-phases"></a>会话阶段

每个会话都会经历多个阶段。

### <a name="session-startup"></a>会话启动

当请求 ARR [创建新会话](../how-tos/session-rest-api.md#create-a-session)时，第一步是返回会话 [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 此 UUID 允许你查询有关会话的信息。 UUID 以及有关会话的一些基本信息将保留 30 天，因此即使在会话停止后，也可以查询这些信息。 此时，“会话状态”将报告为“开始”。

接下来，Azure 远程渲染将尝试查找可托管会话的服务器。 此搜索有两个参数。 首先，它将仅保留你所在[区域](../reference/regions.md)中的服务器。 这是因为跨区域的网络延迟可能太高，因而无法保证良好的体验。 第二个因素是指定的所需大小。 在每个区域中，只有数量有限的服务器可满足“标准”或“高级”大小的请求。 因此，如果所请求大小的所有服务器当前在你的区域中都处于使用中，则会话创建会失败。 [可以查询](../how-tos/session-rest-api.md#get-sessions-properties)失败的原因。

> [!IMPORTANT]
> 如果请求“标准”VM 大小，而请求由于高需求而失败，并不意味着请求“高级”服务器也会失败。 因此选择权在你，你可以尝试回退到“高级”VM。

当服务找到合适的服务器时，需要将适当的虚拟机 (VM) 复制到该服务器上，以将其转化为 Azure 远程渲染主机。 此过程需要几分钟。 然后，VM 将启动，“会话状态”将转变为“就绪” 。

此时，该服务器将专门等待你的输入。 也是从此时开始向你收取服务费用。

### <a name="connecting-to-a-session"></a>连接到会话

会话一旦进入“就绪”状态，，便可以连接到会话。 连接后，设备可以发送命令以加载和修改模型。 每个 ARR 主机一次只提供一台客户端设备，因此，当客户端连接到会话时，它将独占控制所渲染的内容。 这也意味着，渲染性能绝不会因不受控制的原因而有所不同。

> [!IMPORTANT]
> 尽管只有一个客户端可以连接到会话，但可以在不连接的情况下查询有关会话的基本信息，如当前状态。

当设备连接到会话时，其他设备的连接尝试将失败。 但是，一旦连接的设备断开连接（不论是自愿还有由于其他原因），该会话将接受其他连接请求。 所有以前的状态（已加载的模型等）都将被丢弃，以便下一个连接设备重新开始。 因此，可以由不同的设备多次重复使用会话，在大多数情况下，可能会对最终用户隐藏会话启动开销。

> [!IMPORTANT]
> 远程服务器绝不会改变客户端数据的状态。 数据的所有变化（如转换更新和加载请求）都必须由客户端应用程序执行。 所有操作都会立即更新客户端状态。

### <a name="session-end"></a>会话结束

请求新会话时，应指定最大租用时间，通常在 1 到 8 小时范围内。 这是主机接受输入的持续时间。

会话结束的常规原因有两个。 手动请求停止会话或最长租约时间过期。 在这两种情况下，将立即关闭与主机的任何活动连接，并且服务也将在该服务器上关闭。 然后，服务器将回到 Azure 池，并可能重新用于其他用途。 停止会话的操作不能撤消或取消。 在已停止的会话上查询会话状态会返回“停止”或“已过期” ，具体取决于是手动关闭还是达到了最大租用时间。

会话也可能因某些故障而停止。

在所有情况下，会话停止后不会再继续计费。

> [!WARNING]
> 是否连接到会话以及连接多长时间不会影响计费。 为服务支付的费用取决于会话持续时间，也就是服务器的独占预留时间和请求的硬件功能（VM 大小）。 如果启动一个会话，连接 5 分钟，然后不停止该会话，以便在其租用过期之前保持运行状态，将按完整会话租用时间进行计费。 相反，最大租用时间主要起到一个安全网的作用。 如果请求了一个租用时间为 8 小时的会话，仅使用了 5 分钟，此后便手动停止了该会话，也没有关系。

#### <a name="extend-a-sessions-lease-time"></a>延长会话的租用时间

如果需要使用更长时间，可以[延长活动会话的租用时间](../how-tos/session-rest-api.md#update-a-session)。

## <a name="example-code"></a>示例代码

下面的代码演示了一个简单的实现：启动会话，等待进入“就绪”状态，连接，然后再断开连接并关闭。

```cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

可以在代码中维护、操作和查询多个 `AzureFrontend` 和 `AzureSession` 实例。 但一次只有一个设备可以连接到 `AzureSession`。

虚拟机的生存期与 `AzureFrontend` 实例或 `AzureSession` 实例无关。 必须调用 `AzureSession.StopAsync` 才能停止会话。

可以通过 `AzureSession.SessionUUID()` 查询持久会话 ID 并在本地进行缓存。 使用此 ID，应用程序可以调用 `AzureFrontend.OpenSession` 以绑定到该会话。

如果 `AzureSession.IsConnected` 为 true，则 `AzureSession.Actions` 将返回 `RemoteManager` 的实例，该实例包含的函数用于[加载模型](models.md)、操作[实体](entities.md)以及[查询](../overview/features/spatial-queries.md)所渲染的场景的相关信息。

## <a name="next-steps"></a>后续步骤

* [实体](entities.md)
* [模型](models.md)
