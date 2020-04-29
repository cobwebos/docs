---
title: 远程渲染会话
description: 描述远程呈现会话的定义
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681683"
---
# <a name="remote-rendering-sessions"></a>远程渲染会话

在 Azure 远程呈现（ARR）中，*会话*是一个重要概念。 本文介绍了什么是会话的确切内容。

## <a name="overview"></a>概述

Azure 远程呈现的工作方式是将复杂的呈现任务卸载到云中。 不能通过任何服务器来完成这些呈现任务，因为大多数云服务器没有 Gpu。 由于涉及的数据量，以及以交互式帧速率生成结果的硬要求，因此，处理哪些用户请求的责任也不能即时传递到另一台计算机，这可能是更常见的 web 流量。

这意味着，在使用 Azure 远程呈现时，必须以独占方式保留具有必要硬件功能的云服务器来处理呈现请求。 *会话*是指与此服务器交互相关的所有内容。 它从为你使用的保留（*租约*）计算机的初始请求开始，并继续执行加载和操作模型的所有命令，并在云服务器上释放租约结束。

## <a name="managing-sessions"></a>管理会话

可以通过多种方式来管理会话和与会话交互。 创建、更新和关闭会话的与语言无关的方法是通过[会话管理 REST API](../how-tos/session-rest-api.md)。 在 c # 和 c + + 中，这些操作通过`AzureFrontend`类`AzureSession`和公开。 对于 Unity 应用程序，该`ARRServiceUnity`组件还提供了更多实用工具函数。

一旦*连接*到活动会话，[加载模型](models.md)和与场景交互等操作将通过`AzureSession`类公开。

### <a name="managing-multiple-sessions-simultaneously"></a>同时管理多个会话

不能从一个设备完全*连接*到多个会话。 不过，你可以从单个应用程序创建、观察和关闭任意数量的会话。 只要应用程序不会连接到某个会话，就不需要在与 HoloLens 2 相同的设备上运行。 如果希望通过中心机制来控制会话，则可使用此类实现的用例。 例如，可能会构建一个 web 应用，其中，多个平板电脑和 HoloLenses 可以登录。 然后，应用程序可以显示平板电脑上的选项，例如要显示的 CAD 型号。 如果用户进行了选择，则会将此信息传递给所有 HoloLenses，以创建共享体验。

## <a name="session-phases"></a>会话阶段

每个会话都会经历多个阶段。

### <a name="session-startup"></a>会话启动

当您要求 ARR[创建新的会话](../how-tos/session-rest-api.md#create-a-session)时，第一步是返回会话[UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 此 UUID 允许查询有关会话的信息。 有关会话的 UUID 和一些基本信息将保留30天，因此即使在会话停止后，也可以查询该信息。 此时，**会话状态**将报告为 "**正在启动**"。

接下来，Azure 远程呈现尝试查找可托管会话的服务器。 此搜索有两个参数。 首先，它将仅在你的[区域](../reference/regions.md)保留服务器。 这是因为跨区域的网络延迟可能会太高，无法保证获得的体验。 第二个因素是您指定的所需*大小*。 在每个区域中，有有限数量的服务器可满足*标准*或*高级*大小请求。 因此，如果所请求大小的所有服务器当前都在你的区域中使用，则会话创建会失败。 [可以查询](../how-tos/session-rest-api.md#get-sessions-properties)失败的原因。

> [!IMPORTANT]
> 如果请求的是*标准*VM 大小并且请求由于高要求而失败，则不意味着请求*高级*服务器也会失败。 如果是这样的选项，可以尝试回退到*高级*VM。

当服务找到合适的服务器时，它必须将适当的虚拟机（VM）复制到该服务器，以将其转换为 Azure 远程呈现主机。 此过程需要几分钟。 随后将启动 VM，并且**会话状态**将转换为 "**就绪**"。

此时，服务器仅等待输入。 这也是你向其收取服务费用的点。

### <a name="connecting-to-a-session"></a>连接到会话

会话*准备就绪*后，即可*连接*到该会话。 连接后，设备可以发送命令来加载和修改模型。 每个 ARR 主机一次只提供一台客户端设备，因此，当客户端连接到会话时，它将独占控制呈现的内容。 这也意味着，呈现性能绝不会因控件之外的原因而有所不同。

> [!IMPORTANT]
> 尽管仅有一个客户端可以*连接*到会话，但可以在不连接的情况下查询有关会话的基本信息，如其当前状态。

当设备连接到会话时，其他设备进行连接的尝试将会失败。 但是，一旦连接的设备断开连接后，就会自动连接，否则，该会话将接受另一连接请求。 所有以前的状态（已加载的模型等）都将被丢弃，以便下一个连接设备获取干净的石板。 因此，可以通过不同的设备多次重复使用会话，在大多数情况下，可能会隐藏来自最终用户的会话启动开销。

> [!IMPORTANT]
> 远程服务器绝不会改变客户端数据的状态。 所有突变的数据（如转换更新和加载请求）都必须由客户端应用程序执行。 所有操作都会立即更新客户端状态。

### <a name="session-end"></a>会话结束

当你请求新会话时，你指定的*最长租约时间*通常为1到8小时。 这是宿主接受输入的持续时间。

会话结束的原因有两个。 你手动请求停止会话或最长租约时间过期。 在这两种情况下，将立即关闭与主机的任何活动连接，并且该服务器上的服务将关闭。 然后，将服务器传回 Azure 池，并可能会出于其他目的 requisitioned。 停止会话不能撤消或取消。 在已停止的会话上查询**会话状态**将返回 "**已停止**" 或 "已**过期**"，具体取决于是否手动关闭或达到了最长租约时间。

会话也可能因某些故障而停止。

在所有情况下，会话停止后不会再进一步计费。

> [!WARNING]
> 你是连接到会话，还是需要多长时间才会影响计费。 你为服务支付的费用取决于*会话持续*时间，这意味着为你专门保留服务器的时间，以及所请求的硬件功能（VM 大小）。 如果启动一个会话，连接5分钟，然后不停止该会话，以便在其租约过期之前保持运行状态，您将按完整会话租约时间计费。 相反，*最长租约时间*通常是安全网络。 如果你请求的会话的租约时间为8小时，则不重要，如果以后手动停止会话，则仅使用5分钟。

#### <a name="extend-a-sessions-lease-time"></a>延长会话的租约时间

如果有活动会话需要更长时间，可以延长活动会话的[租约时间](../how-tos/session-rest-api.md#update-a-session)。

## <a name="example-code"></a>示例代码

下面的代码演示了启动会话、等待*就绪*状态、连接，然后再次断开和关闭的简单实现。

``` cs
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

可以`AzureFrontend`从`AzureSession`代码中维护、操作和查询多个和实例。 但一次只有一个设备可以连接到`AzureSession` 。

虚拟机的生存期未绑定到`AzureFrontend`实例或`AzureSession`实例。 `AzureSession.StopAsync`必须调用才能停止会话。

可以通过`AzureSession.SessionUUID()`在本地对持久会话 ID 进行查询并将其缓存在本地。 使用此 ID，应用程序可以调用`AzureFrontend.OpenSession`以绑定到该会话。

如果`AzureSession.IsConnected`为 true， `AzureSession.Actions` `RemoteManager`则返回的实例，该实例包含用于[加载模型](models.md)、操作[实体](entities.md)和查询所呈现场景的[信息](../overview/features/spatial-queries.md)的函数。

## <a name="next-steps"></a>后续步骤

* [实体](entities.md)
* [模型](models.md)
