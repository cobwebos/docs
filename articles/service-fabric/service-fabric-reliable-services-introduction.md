<properties
   pageTitle="Service Fabric Reliable Service 编程模型概述"
   description="了解 Service Fabric 的 Reliable Service 编程模型，并开始编写你自己的服务。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="jessebenson; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.date="08/26/2015"
   wacn.date=""/>

# Reliable Services 概述
Service Fabric 可简化可靠的无状态服务和有状态服务的编写与管理。本文档将讨论：

1. 无状态服务和有状态服务的 Reliable Service 编程模型。
2. 编写 Reliable Service 时必须进行的不同选择。
3. 有关 Reliable Services 的使用场合及编写方式的一些不同方案和示例。

Reliable Services 是 Service Fabric 上可用的编程模型之一。有关 Reliable Actors 编程模型的详细信息，请查看[简介](/documentation/articles/service-fabric-reliable-actors-introduction)。

在 Service Fabric 中，服务由配置、应用程序代码和状态（可选）组成。

Service Fabric 通过 [Service Fabric 应用程序管理](/documentation/articles/service-fabric-deploy-remove-applications)管理服务的生存期，从预配和部署一直到升级和删除。

## 什么是 Reliable Services？
Reliable Services 为你提供简单且功能强大的顶级编程模型，以帮助你表达对你的应用程序至关重要的内容。借助 Reliable Services 编程模型，你将获得：

1. 对于有状态服务，Reliable Services 编程模型允许你使用 Reliable Collections 将状态以一致而可靠的方式存储在服务中：Reliable Collections 是一组简单的高可用性集合类，使用过 C# 集合的人都会熟悉它。一直以来，服务需借助外部系统来进行可靠的状态管理。利用 Reliable Collections，你可以将状态存储在计算旁边，并且具有像高可用性外部存储一样的高可用性和可靠性，以及共置计算和状态所提供的额外延迟改善。

2. 运行你自己的代码的简单模型，看起来就像你以前经常使用的编程模型：你的代码具有定义完善的入口点和易于管理的生命周期。

3. 可插入的通信模型 — 使用你选择的传输方式，如包含 [Web API](/documentation/articles/service-fabric-reliable-services-communication-webapi) 的 HTTP、Websockets、自定义 TCP 协议等等。Reliable Services 提供一些很棒的自带选项供你使用，也允许你提供自己的选项。

## Reliable Services 有何不同之处？
Service Fabric 中的 Reliable Services 与你以前编写的服务不同。Service Fabric 提供可靠性、可用性、一致性和可伸缩性。

+ <u>可靠性</u> — 即使在不可靠的环境中（计算机可能出现故障或遇到网络问题），服务仍然能正常运行。

+ <u>可用性</u> — 服务可供访问并且可以响应（这并不表示你不能有找不到或无法从外部访问的服务）。

+ <u>可伸缩性</u> — 服务与特定硬件分离，可以根据需要通过添加或删除硬件或虚拟资源来增大或缩小。可对服务轻松分区（尤其是在有状态的情况下），以确保服务的独立部分可以独立地缩放和响应故障。最后，Service Fabric 鼓励轻量级服务，它允许在单个进程内预配数千个服务，而不需要或让整个 OS 实例专属于特定工作负荷的单个实例。

+ <u>一致性</u> — 这意味着可以保证此服务中存储的任何信息都是一致的（这仅适用于有状态服务 — 后面将对此进行详细介绍）

## 服务生命周期
无论你的服务有状态还是无状态，Reliable Services 都会提供简单的生命周期，可让你快速插入代码并开始执行。事实上，你只需要实现一种或两种方法，即可启动并运行服务。

+ CreateCommunicationListener — 服务将在其中定义它想使用的通信堆栈。通信堆栈（如 [Web API](/documentation/articles/service-fabric-reliable-services-communication-webapi)）定义服务的侦听终结点（客户端将如何访问服务），以及所显示的消息最后如何与服务代码的其余部分交互。

+ RunAsync — 服务将在其中运行自己的业务逻辑。所提供的取消标记是指示该操作何时应停止的信号。例如，如果你的服务需要不断地从 ReliableQueue 中拉取消息并加以处理，这会是开始该操作的位置。

Reliable Service 的生命周期中的主要事件如下所示：

1. 构造服务对象（派生自 StatelessService 或 StatefulService）。

2. 调用 CreateCommunicationListener 方法，使服务可以返回它所选的通信侦听器。
  + 请注意，这是可选方法，虽然大多数的服务会直接公开某个终结点。

3. CommunicationListener 创建后即打开
  + CommunicationListener 具有一个可在此时调用的名为 Open() 的方法，该方法将返回服务的侦听地址。如果 Reliable Service 使用某个内置的 ICommunicationListener，则会为你处理。

4. 对通信侦听器调用 Open() 方法后，即对主服务调用 RunAsync()。
  + 请注意，RunAsync 是可选方法 — 如果服务的所有操作都只直接进行，以响应用户调用，则无需实现 RunAsync()。

关闭服务（删除服务或者只是将其从特定位置移出）时，调用顺序相同：先对 CommunicationListener 调用 Close()，然后取消传递到 RunAsync() 的取消标记。

## 示例服务
了解这一编程模型后，让我们来快速看看两种不同的服务，了解这些部分如何搭配运作。

### 无状态的 Reliable Services
无状态服务是指在服务内实际未维护任何状态，或者其中的状态完全可释放，无需同步、复制、保留或高可用性。

以没有内存的计算器为例，它会接收所有项并同时执行运算。

在这种情况下，由于服务无需处理任何后台任务，因此，服务的 RunAsync() 可以为空。创建计算器服务时，它将返回 CommunicationListener（例如 [Web API](/documentation/articles/service-fabric-reliable-services-communication-webapi)），CommunicationListener 将在某个端口上打开侦听终结点。此侦听终结点将挂接到不同的方法（例如：“Add(n1, n2)”），这些方法将定义计算器的公共 API。

从客户端进行调用时，将调用相应的方法，并且计算器服务会对所提供的数据执行运算并返回结果。它不存储任何状态。

不存储任何内部状态让此示例计算器变得非常简单。但是，大多数服务并不是真正的无状态，而是将状态外部化到某些存储（例如，所有依靠后备存储或缓存来保存会话状态的 Web 应用程序便不是完全无状态的）。

Service Fabric 中常见的无状态服务使用示例是作为前端，它公开 Web 应用程序的面向公众的 API。然后，该前端服务指示有状态服务完成用户的请求。在这种情况下，来自客户端的调用将定向到无状态服务正在侦听的某个已知端口（如 80）。此无状态服务将接收调用，并判断调用是否来自可信方以及其目标服务是哪一个。然后，此无状态服务将调用转发到有状态服务的正确分区并等待响应。收到响应后，它会回复原始客户端。

### 有状态的 Reliable Services
有状态服务是指必须存在状态的某部分并且使该部分保持一致才能正常运行的服务。假设有一个服务不断地根据某个值收到的更新，计算它的滚动平均值。为此，它必须具有目前需要处理的传入请求集以及目前的平均值。检索、处理并将信息存储在外部存储（比如现在的 Azure Blob 或表存储）的任何服务都是有状态的，只不过它将其状态保存在外部状态存储中。

现在的大多数服务将其状态存储在外部，因为外部存储可为该状态提供可靠性、可用性、可伸缩性和一致性。在 Service Fabric 中，有状态服务无需将其状态存储在外部，因为 Service Fabric 会为服务代码和服务状态处理这些需求。

假设我们想要编写一个服务，该服务请求一系列需要对某个映像执行的转换以及需要转换的映像。对于此服务，它将返回一个可打开通信端口并允许通过 `ConvertImage(Image i, IList<Conversion> conversions)` 等 API 进行提交的 CommunicationListener（假设为 WebAPI）。在此 API 中，服务可能获取信息，并将请求存储在 ReliableQueue 中，然后将某个标记返回到客户端，以便其跟踪请求（因为请求可能需要花一些时间）。

在此服务中，RunAsync 可能会更复杂：服务在其 RunAsync 内会有一个循环，即从 ReliableQueue 中拉取请求，执行列出的转换，并将结果存储在 ReliableDictionary 中，以便当客户端返回时可以获取其转换后的映像。为确保即使某部分出现故障，映像也不会丢失，此 Reliable Service 会从 Queue 中拉取请求，执行转换，并将结果存储在 Transaction 中，以便仅当转换完成后，才从队列中真正删除消息并将结果存储在结果字典中。如果某部分中途出现故障（比如运行此代码实例的计算机），请求将保留在队列中，等待再次处理。

关于此服务需要注意的一点是，此服务听起来像是一般的 .NET 服务，唯一的区别是其使用的数据结构（ReliableQueue 和 ReliableDictionary）由 Service Fabric 提供，因此高度可靠、可用且一致。

## 何时使用 Reliable Services API
如果下列任何一项描述了你的应用程序服务需求，便应该考虑使用 Reliable Services API：

- 你需要跨多个状态单元（例如订单和订单明细）提供应用程序行为

- 你的应用程序状态可以自然地建模为 Reliable Dictionary 和 Reliable Queue

- 你的状态必须高度可用并且具有较低的访问延迟

- 你的应用程序需要跨一个或多个 Reliable Collections 控制事务处理操作的并发或粒度

- 你想为服务管理通信或控制分区方案

- 你的代码需要一个自由线程运行时环境

- 你的应用程序需要在运行时动态创建或销毁 Reliable Dictionary 或 Reliable Queue

- 你需要以编程方式为你的服务状态控制 Service Fabric 提供的备份和还原功能*

- 你的应用程序需要维护其状态单元的更改历史记录*

- 你想要开发或使用第三方开发的自定义状态提供程序*

> [AZURE.NOTE]*公开上市版 SDK 将提供这些功能


## 后续步骤
+ [Reliable Services 快速启动](/documentation/articles/service-fabric-reliable-services-quick-start)
+ [查看 Reliable Services 的高级用法](/documentation/articles/service-fabric-reliable-services-advanced-usage)
+ [了解 Reliable Actors 编程模型](/documentation/articles/service-fabric-reliable-actors-introduction)
 

<!---HONumber=74-->