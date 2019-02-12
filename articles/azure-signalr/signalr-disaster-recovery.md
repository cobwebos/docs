---
title: Azure SignalR 服务中的复原能力和灾难恢复
description: 有关如何设置多个 SignalR 服务实例以实现复原能力和灾难恢复的概述
author: chenkennt
ms.service: signalr
ms.topic: overview
ms.date: 01/29/2019
ms.author: kenchen
ms.openlocfilehash: 8852bd5ef9dbe666abebfdc64a31faa135c9b8b6
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569667"
---
# <a name="resiliency-and-disaster-recovery"></a>复原能力和灾难恢复

复原能力和灾难恢复是联机系统的常见需求。 Azure SignalR 服务可保证 99.9% 的可用性，但它仍是一个区域性的服务。
服务实例始终在一个区域中运行，出现区域范围的服务中断时，它不会故障转移到另一个区域。

相反，我们的服务 SDK 提供了相应的功能来支持多个 SignalR 服务实例，当其中的某些实例不可用时，它会自动切换到其他实例。
发生灾难时，可以使用此功能进行恢复，不过，仍然需要自行设置正确的系统拓扑。 本文档将会介绍此操作。

## <a name="high-available-architecture-for-signalr-service"></a>SignalR 服务的高可用性体系结构

若要获得 SignalR 服务的跨区域复原能力，需要在不同的区域中设置多个服务实例。 这样，当某个区域出现故障时，可将其他区域用作备用区域。
将多个服务实例连接到应用服务器时，有两个角色：主要角色和辅助角色。
主要角色是接收联机流量的实例，辅助角色是完全正常运行的实例，但它是主要角色的备用实例。
在 SDK 实现中，协商仅返回主要终结点，因此，在正常情况下，客户端只连接到主要终结点。
但是，当主要实例出现故障时，协商将返回辅助终结点，因此客户端仍可建立连接。
主要实例和应用服务器通过正常的服务器连接进行连接，但辅助实例和应用服务器通过一种称作“弱连接”的特殊连接进行连接。
弱连接的主要区别在于，它不接受客户端连接路由，因为辅助实例通常位于另一个区域中。 将客户端路由到另一个区域通常不是最佳选择（会增大延迟）。

一个服务实例在连接到多个应用服务器时可以有不同的角色。
跨区域方案的一种典型设置是使用两对（或更多对）SignalR 服务实例和应用服务器。
在每一对中，应用服务器和 SignalR 服务位于同一区域，SignalR 服务作为主要角色连接到应用服务器。
在每对之间，应用服务器和 SignalR 服务也会建立连接，但是，在连接到另一区域中的服务器时，SignalR 将变成辅助角色。

使用此拓扑时，来自一台服务器的消息仍可传送到所有客户端，因为所有应用服务器和 SignalR 服务实例是互连的。
但是，客户端在连接后，始终会路由到同一区域中的应用服务器，以实现最佳网络延迟。

下图演示了这种拓扑：

![拓扑](media/signalr-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>使用多个 SignalR 服务实例配置应用服务器

在每个区域中创建 SignalR 服务和应用服务器后，可将应用服务器配置为连接到所有 SignalR 服务实例。

可通过两种方式实现此目的：

### <a name="through-config"></a>通过配置

你应该已经知道如何通过环境变量/应用设置/web.cofig 和名为 `Azure:SignalR:ConnectionString` 的配置项设置 SignalR 服务连接字符串。
如果有多个终结点，可在多个配置项中设置这些终结点，每个项采用以下格式：

```
Azure:SignalR:Connection:<name>:<role>
```

此处的 `<name>` 是终结点的名称，`<role>` 是其角色（主要或辅助）。
名称是可选的，但如果你想要进一步自定义多个终结点之间的路由行为，则名称非常有用。

### <a name="through-code"></a>通过代码

如果你偏向于将连接字符串存储到其他位置，则也可以在代码中读取连接字符串，并在调用 `AddAzureSignalR()`（在 ASP.NET Core 中）或 `MapAzureSignalR()`（在 ASP.NET 中）时将其用作参数。

下面是示例代码：

ASP.NET Core：

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET：

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

## <a name="failover-sequence-and-best-practice"></a>故障转移序列和最佳做法

现已设置正确的系统拓扑。 每当某个 SignalR 服务实例出现故障时，联机流量将路由到其他实例。
下面是当主要实例出现故障（以及一段时间后进行恢复）时发生的情况：

1. 主要服务实例出现故障，此实例上的所有服务器连接将被删除。
2. 连接到此实例的所有服务器会将此实例标记为脱机，协商将停止返回此终结点，并开始返回辅助终结点。
3. 此实例上的所有客户端连接也会关闭，客户端将重新连接。 由于应用服务器现在返回辅助终结点，因此客户端将连接到辅助实例。
4. 现在，辅助实例将接收所有联机流量。 由于辅助实例已连接到所有应用服务器，因此从服务器发往客户端的所有消息仍可传送。 但是，从客户端发往服务器的消息只能路由到同一区域中的应用服务器。
5. 主要实例恢复并重新联机后，应用服务器将与它重新建立连接，并将其标记为联机。 协商现在会再次返回主要终结点，因此，新客户端将重新连接到主要实例。 但是，现有客户端不会被删除，并继续路由到辅助实例，直到它们自行断开连接。

下图演示了 SignalR 服务中如何实现故障转移：

图 1：故障转移之前![故障转移之前](media/signalr-disaster-recovery/before-failover.png)

图 2：故障转移之后![故障转移之后](media/signalr-disaster-recovery/after-failover.png)

图 3：主要实例恢复后的短时间内![主要实例恢复后的短时间内](media/signalr-disaster-recovery/after-recover.png)

可以看到，在正常情况下，只有主要应用服务器和 SignalR 服务包含联机流量（以蓝色表示）。
故障转移后，辅助应用服务器和 SignalR 服务也处于活动状态。
主要 SignalR 服务重新联机后，新客户端将连接到主要 SignalR。 但是，现有客户端仍连接到辅助实例，因此这两个实例都包含流量。
所有现有客户端断开连接后，系统将会恢复正常（图 1）。

可以使用两种主要模式来实现跨区域的高可用性体系结构：

1. 第一种模式是使用一对应用服务器和 SignalR 服务实例来接收所有联机流量，并使用另一对作为备用实例（称为主动/被动配置，如图 1 所示）。 
2. 另一种模式是使用两对（或更多对）应用服务器和 SignalR 服务实例，其中每个实例接收一部分联机流量，并充当其他对的备用实例（称为主动/主动配置，类似于图 3）。

SignalR 服务支持这两种模式，主要差别在于实现应用服务器的方式。
如果应用服务器采用主动/被动配置，则 SignalR 服务也采用主动/被动配置（因为主要应用服务器仅返回其主要 SignalR 服务实例）。
如果应用服务器采用主动/主动配置，则 SignalR 服务也采用主动/主动配置（因为所有应用服务器将返回其自己的主要 SignalR 实例，因此它们都可以接收流量）。

请注意，无论选择使用哪种模式，都需要将每个 SignalR 服务实例作为主要实例连接到应用服务器。

另外，由于 SignalR 连接的性质（远距离连接），发生灾难和故障转移时，客户端会遇到连接断开的情况。
需要在客户端上处理这种情况，使其对最终客户透明。 例如，关闭连接后不要重新连接。

## <a name="next-steps"></a>后续步骤

本文已介绍如何配置应用程序以实现 SignalR 服务的复原能力。 若要更详细地了解 SignalR 服务中的服务器/客户端连接和连接路由，请阅读[此文](signalr-internals.md)，其中介绍了 SignalR 服务的内部情况。