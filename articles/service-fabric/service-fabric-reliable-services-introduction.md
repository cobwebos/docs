---
title: 可靠服务编程模型概述
description: 了解 Service Fabric 的 Reliable Service 编程模型，并开始编写自己的服务。
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: 58259b0d19d68c468779a579bd9c86e77106c18d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083514"
---
# <a name="reliable-services-overview"></a>Reliable Services 概述

Azure 服务结构简化了无状态和有状态服务的编写和管理。 本主题的内容：

* 无状态服务和有状态服务的 Reliable Services 编程模型。
* 编写 Reliable Services 时必须做出选择。
* 有关 Reliable Services 的使用方案及编写方式的一些方案和示例。

*可靠服务*是 Service Fabric 上可用的编程模型之一。 另一个是*可靠参与者*编程模型，它提供了一个[虚拟参与者](https://research.microsoft.com/en-us/projects/orleans/)应用程序框架在可靠服务模型之上。 有关 Reliable Actors 的详细信息，请参阅 [Service Fabric Reliable Actors 简介](service-fabric-reliable-actors-introduction.md)。

Service Fabric 通过 [Service Fabric 应用程序管理](service-fabric-deploy-remove-applications.md)管理服务的生存期，从预配和部署一直到升级和删除。

## <a name="what-are-reliable-services"></a>什么是可靠的服务

Reliable Services 提供简单且功能强大的顶级编程模型，以帮助你表达对应用程序至关重要的内容。 借助 Reliable Services 编程模型，会获得：

* 访问服务结构 API。 与建模为[来宾可执行的](service-fabric-guest-executables-introduction.md)Service Fabric 服务不同，可靠服务可以直接使用服务交换矩阵 API。 这样，服务便可以：
  * 查询系统
  * 报告群集中实体的运行状况
  * 接收有关配置和代码更改的通知
  * 查找并与其他服务通信，
  * 使用[可靠的集合](service-fabric-reliable-services-reliable-collections.md)
  * 访问许多其他功能，全部来自多个编程语言的一流编程模型。
* 用于运行您自己的代码的简单模型，感觉就像其他熟悉的编程模型。 代码具有定义完善的入口点和易于管理的生命周期。
* 可插式通信模型。 使用选择的传输方式，如包含 [Web API](service-fabric-reliable-services-communication-webapi.md) 的 HTTP、WebSockets、自定义 TCP 协议，等等。 Reliable Services 提供一些极佳的自带选项供你使用，也可以提供自己的选项。
* 对于有状态服务，Reliable Services 编程模型允许使用 [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) 直接在服务内以一致、可靠的方式存储状态。 Reliable Collections 是一组简单的高度可用、可靠集合类，用过 C# 集合的用户都对它很熟悉。 一直以来，服务需借助外部系统来进行可靠的状态管理。 利用 Reliable Collections，可将状态存储在计算旁边，获得高可用性外部存储一样的高可用性和可靠性。 此模型还能改善延迟问题，因为可将运行此模型所需的计算资源与状态放置在一起。

## <a name="what-makes-reliable-services-different"></a>使可靠服务与众不同的原因

可靠服务与您以前编写的服务不同，因为服务交换矩阵提供：

* **可靠性** - 即使在不可靠的环境中（计算机可能出现故障或遇到网络问题），或者在服务本身遇到错误和崩溃或故障的情况下，服务也仍能正常运行。 对于有状态服务，即使遇到网络故障或其他故障，状态仍会得到保留。
* **可用性** - 服务可供访问，保持响应能力。 Service Fabric 将保留所需数目的运行副本。
* **可伸缩性** - 服务与特定硬件分离，可根据需要通过添加或删除硬件或其他资源来增大或缩小。 轻松将服务分区（尤其是在有状态的情况下），确保服务能够缩放，处理部分故障。 可以通过代码动态创建和删除服务，从而根据需要启动更多实例，例如响应客户请求。 最后，Service Fabric 鼓励轻量级服务。 Service Fabric 允许在单个进程内预配数千个服务，而不要求将整个 OS 实例或进程专用于服务的单个实例。
* **一致性**- 可靠服务中存储的任何信息都可以保证一致。 即使在服务中的多个可靠集合中也是如此。 对服务中的集合所做的更改可通过事务上不可部分完成的方式进行。

## <a name="service-lifecycle"></a>服务生命周期

无论服务有状态还是无状态，Reliable Services 都会提供简单的生命周期，可让你快速插入代码并开始执行。  启动并运行新服务需要实现两种方法：

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - 服务在此方法中定义要使用的通信堆栈。 通信堆栈（如 [Web API](service-fabric-reliable-services-communication-webapi.md)）可定义服务的一个或多个侦听终结点（客户端将如何访问服务）。 它还定义所显示的消息如何与服务代码的其余部分交互。
* **RunAsync** - 服务在此方法中运行其业务逻辑，对于在服务生存期内一直运行的所有后台任务，服务可在此方法中启动这些任务。 所提供的取消标记是指示该操作何时应停止的信号。 例如，如果服务需要从 Reliable Queue 中提取消息并进行处理，这就是这些工作的发生位置。

如果这是首次学习可靠服务，请继续阅读！ 如果您正在寻找可靠服务生命周期的详细演练，请查看[可靠服务生命周期概述](service-fabric-reliable-services-lifecycle.md)。

## <a name="example-services"></a>示例服务

让我们仔细看看可靠服务模型如何与无状态和有状态的服务一起工作。

### <a name="stateless-reliable-services"></a>无状态的 Reliable Services

*无状态服务*是在服务中跨调用中未维护状态的服务。 现有的任何状态完全可释放，无需同步、复制、保留或高可用性。

以没有内存的计算器为例，它会接收所有项并同时执行运算。

在这种情况下，由于服务无需处理任何后台任务，因此，服务的 `RunAsync()` (C#) 或 `runAsync()` (Java) 可为空。 创建计算器服务后，该服务将返回 `ICommunicationListener` (C#) 或 `CommunicationListener`（例如 [Web API](service-fabric-reliable-services-communication-webapi.md)），用于在某个端口上打开侦听终结点。 此侦听终结点挂接到不同的计算方法（例如："Add(n1, n2)"），这些方法定义计算器的公共 API。

从客户端进行调用时，将调用相应的方法，并且计算器服务会对所提供的数据执行运算并返回结果。 它不存储任何状态。

不存储任何内部状态让此示例计算器变得十分简单。 不过大多数服务并不是真正的无状态。 它们是将状态外部化到其他某个存储。 （例如，任何依赖在备份存储或缓存中保留会话状态的 Web 应用程序便不是无状态的。）

Service Fabric 中常见的无状态服务使用示例是作为前端，它公开 Web 应用程序的面向公众的 API。 然后，前端服务指示有状态服务完成用户请求。 在这种情况下，来自客户端的调用将定向到无状态服务正在侦听的某个已知端口（如 80）。 此无状态服务将接收调用，并判断调用是否来自可信方以及其目标服务是哪一个。  然后，此无状态服务将调用转发到有状态服务的正确分区并等待响应。 无状态服务收到响应后，将回复原始客户端。 此类服务的一个示例是*服务结构入门*示例 （[C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)），以及该回购中的其他服务结构示例。

### <a name="stateful-reliable-services"></a>有状态的 Reliable Services

*有状态服务*是必须使状态的某些部分保持一致和存在才能使服务正常运行的服务。 假设有一个服务不断地根据某个值收到的更新，计算它的滚动平均值。 为此，它必须具有目前需要处理的传入请求集以及目前的平均值。 检索、处理并将信息存储在外部存储（比如现在的 Azure Blob 或表存储）的任何服务都是有状态的， 只不过它将其状态保存在外部状态存储中。

现在的大多数服务将其状态存储在外部，因为外部存储可为该状态提供可靠性、可用性、伸缩性和一致性。 在 Service Fabric 中，服务无需将其状态存储在外部。 Service Fabric 将为服务代码和服务状态处理这些要求。

假设我们要编写一个服务来处理映像。 为此，该服务将提取一个映像，然后针对该映像执行一系列转换。 此服务将返回一个可公开 API（如 `ConvertImage(Image i, IList<Conversion> conversions)`）的通信侦听器（假设为 Web API）。 当它收到请求时，服务将其存储在 中`IReliableQueue`，并将一些 ID 返回给客户端，以便它可以跟踪请求。

在此服务中，`RunAsync()` 可能更复杂。 服务在其 `RunAsync()` 内部使用一个循环从 `IReliableQueue` 中提取请求并执行请求的转换。 结果存储在 `IReliableDictionary` 中，以便当客户端返回时可以获取其转换后的映像。 为了确保即使发生故障映像也不丢失，此 Reliable Services 将从队列提取数据、执行转换，然后将整个结果存储在事务中。 在此情况下，仅当转换完成时，才会从队列中删除消息并将结果存储在结果字典中。 或者，服务可从队列中提取映像，并立即将其存储在远程存储中。 这可以减少服务必须管理的状态数量，但会增大复杂性，因为服务必须保留必要的元数据来管理远程存储。 不管使用哪种方法，如果某个环节在中途失败，请求将保留在队列中等待处理。

尽管此服务听起来像典型的 .NET 服务，但区别在于所使用的数据结构 （`IReliableQueue`和`IReliableDictionary`） 由 Service Fabric 提供，并且非常可靠、可用且一致。

## <a name="when-to-use-reliable-services-apis"></a>何时使用 Reliable Services API

请考虑可靠的服务 API，如果：

* 您希望服务的代码（以及可选状态）高度可用且可靠。
* 需要跨多个状态单元（例如订单和订单明细）提供事务保证。
* 应用程序状态可以自然地建模为可靠字典和可靠队列。
* 应用程序代码或状态需要高度可用，读写延迟较低。
* 应用程序需要跨一个或多个可靠集合控制事务处理操作的并发或粒度。
* 想为服务管理通信或控制分区方案。
* 代码需要一个自由线程运行时环境。
* 应用程序需要在运行时动态创建或销毁 Reliable Dictionaries、队列或整个服务。
* 需要以编程方式为服务状态控制 Service Fabric 提供的备份和还原功能。
* 应用程序需要维护其状态单元的更改历史记录。
* 想要开发或使用第三方开发的自定义状态提供程序。

## <a name="next-steps"></a>后续步骤

* [Reliable Services 快速启动](service-fabric-reliable-services-quick-start.md)
* [Reliable collections](service-fabric-reliable-services-reliable-collections.md)
* [Reliable Actors 编程模型](service-fabric-reliable-actors-introduction.md)
