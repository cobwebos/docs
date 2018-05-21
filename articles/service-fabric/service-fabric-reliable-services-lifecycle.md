---
title: Azure Service Fabric Reliable Services 生命周期概述 | Microsoft 文档
description: 了解 Service Fabric Reliable Services 中的不同生命周期事件
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: ''
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 42833323cbebf25ce2ca14e6ab7ec4fa5adbfd15
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-services-lifecycle-overview"></a>Reliable Services 生命周期概述
> [!div class="op_single_selector"]
> * [Windows 上的 C#](service-fabric-reliable-services-lifecycle.md)
> * [Linux 上的 Java](service-fabric-reliable-services-lifecycle-java.md)
>
>

在考虑要 Azure Service Fabric Reliable Services 的生命周期时，具备生命周期的基础知识这一点极为重要。 一般而言，生命周期包括：

- 在启动期间：
  - 构造服务。
  - 服务可能会构造并返回零个或多个侦听器。
  - 打开返回的所有侦听器，以便与服务通信。
  - 调用服务的 **RunAsync** 方法，使服务能够执行长时间运行的任务或后台工作。
- 在关闭期间：
  - 取消传递给 **RunAsync** 的取消令牌，同时关闭侦听器。
  - 侦听器关闭后，销毁服务对象本身。

有相应的文档详细介绍了有关这些事件的确切顺序。 事件的顺序可能会根据 Reliable Service 是无状态服务还是有状态服务而略有变化。 此外，对于有状态服务，必须处理主副本交换方案。 在执行此序列期间，主副本的角色将转移到另一个副本（或者转移回来），而无需关闭服务。 最后，必须考虑到错误或失败状态。

## <a name="stateless-service-startup"></a>无状态服务启动
无状态服务的生命周期非常直接了当。 下面是事件的顺序：

1. 构造服务。
2. 然后，并行发生两个事件：
    - 调用 `StatelessService.CreateServiceInstanceListeners()`，打开返回的所有侦听器。 对每个侦听器调用 `ICommunicationListener.OpenAsync()`。
    - 调用服务的 `StatelessService.RunAsync()` 方法。
3. 如果存在，则调用服务的 `StatelessService.OnOpenAsync()` 方法。 此调用是一种不常见的重写，但是可行的。 此时可以启动扩展的服务初始化任务。

请注意，用于创建和打开侦听器与 **RunAsync** 的调用之间没有一定的顺序。 可在启动 **RunAsync** 之前打开侦听器。 同样，在通信侦听器打开甚至构造之前，即可调用 **RunAsync**。 如果需要进行任何同步，这是实现器的任务。 下面是一些常见的解决方案：

  - 有时，在创建其他某些信息或者完成工作之前，侦听器无法正常运行。 对于无状态服务，该操作通常可在其他位置完成，例如： 
    - 在服务的构造函数中。
    - 在 `CreateServiceInstanceListeners()` 调用期间。
    - 作为侦听器本身构造的一部分。
  - 有时，在侦听器打开之前，**RunAsync** 中的代码不会启动。 在这种情况下，必须进行更多的协调。 一种常见的解决方案是在侦听器内使用某些标志指示它们完成的时间。 此标志签入 **RunAsync**，然后继续执行实际工作。

## <a name="stateless-service-shutdown"></a>无状态服务关闭
关闭无状态服务时遵循相同的模式，只不过遵循的顺序相反：

1. 并行：
    - 关闭任何打开的侦听器。 对每个侦听器调用 `ICommunicationListener.CloseAsync()`。
    - 取消传递给 `RunAsync()` 的取消令牌。 检查取消令牌的 `IsCancellationRequested` 属性是否返回 true，如果调用令牌的 `ThrowIfCancellationRequested` 方法，则会引发 `OperationCanceledException`。
2. 如果存在，则在针对每个侦听器完成 `CloseAsync()` 并且完成 `RunAsync()` 后，调用服务的 `StatelessService.OnCloseAsync()` 方法。  当要正常关闭无状态服务实例时调用 OnCloseAsync。 升级服务代码、由于负载均衡而移动服务实例或是检测到暂时性故障时，可能会出现这种情况。 重写 `StatelessService.OnCloseAsync()` 并不常见，但它可以用于安全地关闭资源、停止后台处理、完成外部状态保存或关闭现有连接。
3. 完成 `StatelessService.OnCloseAsync()` 后，销毁服务对象。

## <a name="stateful-service-startup"></a>有状态服务启动
有状态服务的模式与无状态服务类似，只是稍有不同。 启动有状态服务时，事件的顺序如下所述：

1. 构造服务。
2. 调用 `StatefulServiceBase.OnOpenAsync()`。 此调用是服务中不常见的重写。
3. 以下情况将并行发生：
    - 调用 `StatefulServiceBase.CreateServiceReplicaListeners()`。 
      - 如果服务是主要服务，则打开所有返回的侦听器。 对每个侦听器调用 `ICommunicationListener.OpenAsync()`。
      - 如果服务是辅助服务，则只打开已标记为 `ListenOnSecondary = true` 的侦听器。 在辅助服务上打开的侦听器较不常见。
    - 如果该服务目前是主要服务，则调用该服务的 `StatefulServiceBase.RunAsync()` 方法。
4. 所有副本侦听器的 `OpenAsync()` 调用完成并已调用 `RunAsync()` 后，将调用 `StatefulServiceBase.OnChangeRoleAsync()`。 此调用是服务中不常见的重写。

类似于无状态服务，创建和打开侦听器的顺序以及调用 **RunAsync** 的时间不会经过协调。 如果需要协调，解决方法大致相同。 对于有状态服务，还存在一种情况。 假设抵达通信侦听器的调用需要在某个 [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) 中保存信息。 由于通信侦听器可能在 Reliable Collections 可读或可写之前打开，因此，在 **RunAsync** 可以启动之前，必须经过一定的附加协调。 最简单且最常见的解决方法是让通信侦听器返回错误代码，告知客户端重试请求。

## <a name="stateful-service-shutdown"></a>有状态服务关闭
与无状态服务一样，关闭期间的生命周期事件与启动期间是相同的，但顺序相反。 关闭有状态服务时，将发生以下事件：

1. 并行：
    - 关闭任何打开的侦听器。 对每个侦听器调用 `ICommunicationListener.CloseAsync()`。
    - 取消传递给 `RunAsync()` 的取消令牌。 检查取消令牌的 `IsCancellationRequested` 属性是否返回 true，如果调用令牌的 `ThrowIfCancellationRequested` 方法，则会引发 `OperationCanceledException`。
2. 在针对每个侦听器完成 `CloseAsync()` 并且完成 `RunAsync()` 后，调用服务的 `StatefulServiceBase.OnChangeRoleAsync()`。 此调用是服务中不常见的重写。

   > [!NOTE]  
   > 仅当此副本是主副本时，才需要等待 **RunAsync** 完成。

3. `StatefulServiceBase.OnChangeRoleAsync()` 方法完成后，调用 `StatefulServiceBase.OnCloseAsync()` 方法。 此调用是一种不常见的重写，但是可行的。
3. 完成 `StatefulServiceBase.OnCloseAsync()` 后，销毁服务对象。

## <a name="stateful-service-primary-swaps"></a>有状态服务主副本交换
运行有状态服务时，只有该有状态服务的主副本打开其通信侦听器并调用其 **RunAsync** 方法。 会构造辅助副本，但不会对其执行进一步的调用。 在运行有状态服务时，当前用作主副本的副本可能会更改。 从副本看到的生命周期事件角度看，这意味着什么呢？ 有状态副本看到的行为取决于在交换期间该副本是已降级还是已升级。

### <a name="for-the-primary-thats-demoted"></a>对于已降级的主副本
对于已降级的主副本，Service Fabric 需要使用此副本来停止处理消息，退出正在执行的任何后台工作。 因此，此步骤类似于关闭服务时的情况。 一个差别在于，在此情况下不会销毁或关闭服务，因为它保留为辅助副本。 将调用以下 API：

1. 并行：
    - 关闭任何打开的侦听器。 对每个侦听器调用 `ICommunicationListener.CloseAsync()`。
    - 取消传递给 `RunAsync()` 的取消令牌。 检查取消令牌的 `IsCancellationRequested` 属性是否返回 true，如果调用令牌的 `ThrowIfCancellationRequested` 方法，则会引发 `OperationCanceledException`。
2. 在针对每个侦听器完成 `CloseAsync()` 并且完成 `RunAsync()` 后，调用服务的 `StatefulServiceBase.OnChangeRoleAsync()`。 此调用是服务中不常见的重写。

### <a name="for-the-secondary-thats-promoted"></a>对于已提升的辅助副本
同样，Service Fabric 需要已提升的辅助副本来开始侦听网络上的消息，并启动需要完成的任何后台任务。 因此，此过程类似于创建服务时的情况，只不过副本本身已存在。 将调用以下 API：

1. 并行：
    - 调用 `StatefulServiceBase.CreateServiceReplicaListeners()`，打开返回的所有侦听器。 对每个侦听器调用 `ICommunicationListener.OpenAsync()`。
    - 调用服务的 `StatefulServiceBase.RunAsync()` 方法。
2. 所有副本侦听器的 `OpenAsync()` 调用完成并已调用 `RunAsync()` 后，将调用 `StatefulServiceBase.OnChangeRoleAsync()`。 此调用是服务中不常见的重写。

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>有状态服务关闭和主副本降级期间的常见问题
Service Fabric 更改有状态服务的主副本的原因有多种。 最常见的原因是[群集重新平衡](service-fabric-cluster-resource-manager-balancing.md)和[应用程序升级](service-fabric-application-upgrade.md)。 在这些操作的过程中（以及在普通服务关闭过程中，比如你会了解是否已删除服务），服务必须遵从 `CancellationToken`。 

如果服务不完全处理取消，可能会导致若干问题。 这些操作的速度之所以缓慢，是因为 Service Fabric 要等待服务正常停止。 最终导致升级超时失败，然后回滚。 未能遵循取消令牌也可能导致群集不均衡。 群集之所以不均衡，是因为节点变热，但将它们移到其他位置耗时过长，因此无法重新均衡服务。 

由于服务有状态，它们也可能使用 [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)。 在 Service Fabric 中，主副本降级后，首先会撤销基础状态的写入访问权限。 这会导致可能影响服务生命周期的另外一系列问题。 集合将根据计时和是否已移动或关闭副本返回异常。 应正确处理这些异常。 由 Service Fabric 引发的异常分为永久性 [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) 和暂时性 [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) 类别。 永久性异常应记录并引发，而暂时性异常可以基于某种重试逻辑重试。

处理因结合使用 `ReliableCollections` 和服务生命周期事件而产生的异常是测试和验证 Reliable Service 的重要环节。 建议在执行升级和[混沌测试](service-fabric-controlled-chaos.md)时始终低负载运行服务，然后再部署到生产环境。 以下基本步骤有助于确保已正确实现服务并正确处理生命周期事件。


## <a name="notes-on-the-service-lifecycle"></a>有关服务生命周期的说明
  - `RunAsync()` 方法和 `CreateServiceReplicaListeners/CreateServiceInstanceListeners` 调用都是可选的。 服务可能使用其中的一个或两个，或者都不使用。 例如，如果服务执行的所有工作都只是为了响应用户调用，则无需实现 `RunAsync()`。 只需提供通信侦听器及其关联的代码。 同样，创建和返回通信侦听器的操作也是可选的，因为服务可能只需执行后台工作，在这种情况下，只需实现 `RunAsync()`。
  - 服务成功完成 `RunAsync()` 并从中返回即可。 完成不是失败条件。 完成 `RunAsync()` 表示该服务的后台工作已完成。 对于有状态可靠服务，如果副本已从主副本降级为次要副本，然后重新升级为主副本，则会再次调用 `RunAsync()`。
  - 如果服务因引发某种意外的异常从 `RunAsync()` 退出，将导致失败。 已关闭服务对象，并已报告运行状况错误。
  - 虽然从这些方法返回没有时间限制，但会立即丧失写入到可靠集合的能力，并因此无法完成任何实际工作。 建议尽快在收到取消请求后返回。 如果服务在合理的时间内未响应这些 API 调用，Service Fabric 可能会强行终止服务。 通常，只有在应用程序升级期间或删除服务时，才发生这种情况。 此超时默认为 15 分钟。
  - `OnCloseAsync()` 路径中的失败会导致调用 `OnAbort()`，服务可以凭借这最后一个机会，尽最大努力清理并释放它们所占用的资源。 当在节点上检测到永久性故障时，或者当 Service Fabric 由于内部错误而无法可靠地管理服务实例的生命周期时，通常会调用此方法。
  - 当有状态服务副本要更改角色（例如，更改为主要副本或次要副本）时，调用 `OnChangeRoleAsync()`。 主副本将指定为写状态（允许创建和写入可靠集合）。 辅助副本将指定为读取状态（只能从现有的可靠集合读取）。 有状态服务中的大部分工作在主副本执行。 辅助副本可执行只读验证、报表生成、数据挖掘或其他只读作业。

## <a name="next-steps"></a>后续步骤
- [Reliable Services 简介](service-fabric-reliable-services-introduction.md)
- [Reliable Services 快速启动](service-fabric-reliable-services-quick-start.md)
- [副本和实例](service-fabric-concepts-replica-lifecycle.md)
