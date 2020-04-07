---
title: 远程渲染会话
description: 描述什么是远程呈现会话
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681683"
---
# <a name="remote-rendering-sessions"></a>远程渲染会话

在 Azure 远程呈现 （ARR） 中，*会话*是一个关键概念。 本文介绍了会话的确切内容。

## <a name="overview"></a>概述

Azure 远程呈现的工作原理是将复杂的呈现任务卸载到云中。 这些呈现任务不能仅仅由任何服务器完成，因为大多数云服务器没有 GPU。 由于涉及的数据量和以交互帧速率生成结果的硬性要求，因此用户请求的服务器处理的责任不能动态地移交给另一台计算机，因为对于更常见的 Web 流量，可能是可能的。

这意味着，在使用 Azure 远程呈现时，必须专门保留具有必要硬件功能的云服务器来处理呈现请求。 *会话*是指与此服务器交互所涉及的所有内容。 它从初始请求保留（*租赁*）一台计算机供您使用，继续加载和操作模型的所有命令，最后在云服务器上释放租约。

## <a name="managing-sessions"></a>管理会话

管理和与会话交互的方法有多种。 创建、更新和关闭会话的独立于语言的方法是通过[会话管理 REST API。](../how-tos/session-rest-api.md) 在 C# 和 C++ 中，这些操作`AzureFrontend`通过`AzureSession`类和 公开。 对于 Unity 应用程序，`ARRServiceUnity`组件提供了其他实用程序函数。

*连接到*活动会话后，加载[模型](models.md)和与场景交互等操作将通过`AzureSession`类公开。

### <a name="managing-multiple-sessions-simultaneously"></a>同时管理多个会话

无法从一台设备完全*连接到*多个会话。 但是，您可以创建、观察和关闭从单个应用程序中根据需要创建的会话数。 只要应用程序不用于连接到会话，它也不需要在 HoloLens 2 等设备上运行。 如果要通过中心机制控制会话，则此类实现的用例可能是。 例如，可以构建一个 Web 应用程序，其中多个平板电脑和 HoloLense 可以登录。 然后，应用程序可以在平板电脑上显示选项，例如要显示的 CAD 模型。 如果用户进行选择，此信息将传达给所有 HoloLenses 以创建共享体验。

## <a name="session-phases"></a>会话阶段

每届会议都经历多个阶段。

### <a name="session-startup"></a>会话启动

当您要求 ARR[创建新会话时](../how-tos/session-rest-api.md#create-a-session)，它做的第一件事是返回会话[UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 此 UUID 允许您查询有关会话的信息。 UUID 和一些有关会话的基本信息将保留 30 天，因此即使在会话停止后，您也可以查询该信息。 此时，**会话状态**将报告为 **"开始**"。

接下来，Azure 远程呈现尝试查找可以承载会话的服务器。 此搜索有两个参数。 首先，它只会在您所在[地区](../reference/regions.md)保留服务器。 这是因为跨区域的网络延迟可能太高，无法保证体面的体验。 第二个因素是指定所需的*大小*。 在每个区域中，可以满足*标准*或*高级*大小请求的服务器数量有限。 因此，如果您所在区域当前正在使用请求大小的所有服务器，则会话创建将失败。 [可以查询](../how-tos/session-rest-api.md#get-sessions-properties)失败的原因。

> [!IMPORTANT]
> 如果请求*标准*VM 大小，并且请求由于高需求而失败，这并不意味着请求*高级*服务器也会失败。 因此，如果它是一个选项，你可以尝试回落到*高级*VM。

当服务找到合适的服务器时，它必须将适当的虚拟机 （VM） 复制到它上，才能将其转换为 Azure 远程呈现主机。 此过程需要几分钟。 之后，VM 被启动，**会话状态**将转换为 **"就绪"。**

此时，服务器将专门等待您的输入。 这也是从中您获得服务费用的点。

### <a name="connecting-to-a-session"></a>连接到会话

会话*准备就绪*后，即可*连接到*它。 连接时，设备可以发送命令以加载和修改模型。 每个 ARR 主机一次只提供一个客户端设备，因此当客户端连接到会话时，它可独占对呈现的内容进行控制。 这也意味着渲染性能永远不会因无法控制的原因而发生变化。

> [!IMPORTANT]
> 尽管只有一个客户端可以*连接到*会话，但无需连接即可查询有关会话的基本信息，例如其当前状态。

当设备连接到会话时，其他设备连接的尝试将失败。 但是，一旦连接的设备自愿断开连接，或者由于某种故障，会话将接受另一个连接请求。 所有以前的状态（加载的模型等）都会被丢弃，以便下一个连接设备获得干净的石板。 因此，会话可以由不同的设备多次重复使用，在大多数情况下，可能会向最终用户隐藏会话启动开销。

> [!IMPORTANT]
> 远程服务器永远不会更改客户端数据的状态。 所有数据突变（如转换更新和加载请求）必须由客户端应用程序执行。 所有操作立即更新客户端状态。

### <a name="session-end"></a>会话结束

请求新会话时，指定*最大租约时间*，通常在 1 到 8 小时之间。 这是主机接受输入的持续时间。

会话结束有两个常规原因。 您手动请求停止会话或最大租约时间过期。 在这两种情况下，与主机的任何活动连接都会马上关闭，并且该服务将关闭该服务器上。 然后，服务器将返回 Azure 池，并可能出于其他目的被征用。 无法撤消或取消停止会话。 查询已停止会话上的**会话状态**将返回 **"已停止"** 或 **"已过期**"，具体取决于是手动关闭还是由于已达到最大租约时间。

由于某些故障，会话也可能停止。

在所有情况下，一旦会话停止，您将不会被进一步计费。

> [!WARNING]
> 您是否连接到会话以及多长时间，不会影响计费。 您为服务支付的费用取决于*会话持续时间*，这意味着服务器专门为您保留的时间以及请求的硬件功能（VM 大小）。 如果启动会话，连接五分钟，然后不停止会话，以便会话一直运行，直到其租约到期，您将为整个会话租赁时间计费。 相反，*最长租赁时间*主要是安全网。 是否请求租用时间为 8 小时的会话并不重要，如果之后手动停止会话，则仅使用五分钟。

#### <a name="extend-a-sessions-lease-time"></a>延长会话的租约时间

如果事实证明需要活动会话的[租约时间](../how-tos/session-rest-api.md#update-a-session)较长，则可以延长它的时间。

## <a name="example-code"></a>示例代码

下面的代码显示了启动会话、等待*就绪*状态、连接，然后再次断开连接和关闭的简单实现。

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

可以从`AzureFrontend`代码`AzureSession`维护、操作和查询多个和实例。 但是，一次只能连接到 一`AzureSession`个设备。

虚拟机的生存期不绑定到`AzureFrontend`实例或`AzureSession`实例。 `AzureSession.StopAsync`必须调用以停止会话。

持久会话 ID 可通过本地`AzureSession.SessionUUID()`查询和缓存。 使用此 ID，应用程序可以调用`AzureFrontend.OpenSession`以绑定到该会话。

如果`AzureSession.IsConnected`为`AzureSession.Actions`true，则返回`RemoteManager`的实例，其中包含[加载模型](models.md)、操作[实体](entities.md)和查询有关渲染场景[的信息](../overview/features/spatial-queries.md)的函数。

## <a name="next-steps"></a>后续步骤

* [实体](entities.md)
* [模型](models.md)
