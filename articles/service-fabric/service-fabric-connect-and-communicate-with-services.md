<properties
   pageTitle="Microsoft Azure Service Fabric 如何与服务进行通信"
   description="本文介绍如何连接到并与 Service Fabric 应用程序中的服务进行通信。"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/21/2015"
   wacn.date=""/>


# 与服务通信
在微服务世界，总体解决方案由多种多样的服务组成，每种服务各自执行专项任务。这些微服务相互通信以实现端对端工作流。其中也有连接到服务并与之通信的客户端应用程序。本文档讨论 Service Fabric 如何让你更轻松地与使用 Service Fabric 编写的服务建立通信。

## 关键概念
与服务建立通信时需注意这些关键概念。
### 通信可表示为客户端-服务器
Service Fabric 通信 API 表示客户端-服务类型的交互，即使交互发生在同一群集中运行的两个服务之间。将从客户端连接到的目标服务或其他服务可作为服务器并侦听传入的请求。而可以是群集中另一个服务的客户端将连接到该服务器并对其进行调用。
### 服务的移动
在分布式系统中，出于各种原因（例如，为资源平衡配置加载度量值时出现的负载平衡，或者由于升级、故障转移、横向扩展和其他各种情况），所运行的服务实例可能会随着时间的推移从一台计算机移到另一台计算机。服务的移动使得服务实例的终结点地址可进行更改，并且需要执行以下循环才能与服务实例建立通信。如果使用 Service Fabric 提供的通信 API，则将向你提取这些详细信息。

* **解析**：Service Fabric 中的所有服务实例均具有唯一的 URI，如“fabric:/MyApplication/MyService”，并且这些 URI 不会随时间发生改变。每个服务实例还会公开移动服务实例时可以更改的终结点。这与 URL 保持不变但 IP 可能会改变的网站类似。并且与万维网上将网站 URL 解析为 IP 地址的 DNS 类似，Service Fabric 平台也具有将 URL 解析为终结点的系统服务。此步骤涉及将服务实例的 URI 解析为终结点。

* **连接**：将服务 URI 解析为终结点地址后，下一步则是尝试与该服务进行连接。如果终结点地址因服务移动而更改（例如，这可能由计算机发生故障或因负载平衡而引起），则此连接可能会失败。

* **重试**：如果连接尝试失败，则需重试先前的解析和连接步骤，并且重复此循环，直到连接成功。

## 决定要使用的通信 API
作为 Service Fabric 的一部分，我们提供了几个不同的通信 API 选项，你可以根据所选的编程模型、通信框架和编写服务时使用的编程语言来决定哪个选项最适合自己。
### Reliable Actors 的通信
对于使用 Reliable Actors API 编写的服务，将提取所有通信详细信息，且通信发生在调用 ActorProxy 上的方法时，你可以在此处停止阅读。

### Reliable Services 的通信选项
如果你的服务使用 Reliable Services API 编写，则有几个不同的选项。选择通信协议后可获得指导，例如可以使用哪些 Service Fabric 通信 API。

* **我没有具体需要使用的通信协议，我需要可以快速启动并运行的协议**：如果没有特定的通信框架选择，则你理想之选是使用[默认堆栈](documentation/articles/service-fabric-reliable-services-communication-default)，此协议可实现与 Actor 通信模型类似的模型。这是服务通信入门最简单、最快捷的方法。它提供了已强类型化的 RPC 通信，以便提取出大部分的通信详细信息，从而使代码中远程服务上的方法调用与本地对象实例上的方法调用类似。这些类将在设置通信通道时提取解决方法、连接、重试和错误处理，并可实现基于方法调用的通信模型。为此，请将 `ServiceCommunicationListener` 类用于服务器端，将 `ServiceProxy` 类用于通信的客户端。

* **HTTP**：若要利用基于 HTTP 的通信所提供的灵活性，你可以使用 Service Fabric 通信 API，它允许在继续提取解决方法、连接和重试逻辑时定义通信机制。例如，可将 Web API 用于指定通信机制，并利用 [`ICommunicationClient` 和 `ServicePartitionClient` 类](documentation/articles/service-fabric-reliable-services-communication)来建立通信。
* **WCF**：如果具备将 WCF 用作通信框架的现有代码，则可将 WcfCommunicationListener 用于服务器端，并将 WcfCommunicationClient 和 ServicePartitionClient 类用于客户端。有关详细信息，请参阅[本文](documentation/articles/service-fabric-reliable-services-communication-wcf)。

* **包括自定义协议的其他通信框架**：如果打算使用包括自定义通信协议的任何其他通信框架，则可以使用 Service Fabric 通信 API，它能在提取所有待发现和待连接工作时插入通信堆栈。请参阅[本文](documentation/articles/service-fabric-reliable-services-communication)了解详细信息。

### 以不同的编程语言编写的服务之间的通信
所有 Service Fabric 通信 API 目前均仅在 C# 中可用，因此，如果你的服务采用其他编程语言（如 Java 或 Node.JS）编写，则需要编写自己的通信机制。

## 后续步骤
* [Reliable Services 框架提供的默认通信堆栈](documentation/articles/service-fabric-reliable-services-communication-default)
* [Reliable Services 通信模型](documentation/articles/service-fabric-reliable-services-communication)
* [带 OWIN 自托管的 Microsoft Azure Service Fabric Web API 服务入门](documentation/articles/service-fabric-reliable-services-communication-webapi)
* [Reliable Services 基于 WCF 的通信堆栈](documentation/articles/service-fabric-reliable-services-communication-wcf)
 

<!---HONumber=74-->