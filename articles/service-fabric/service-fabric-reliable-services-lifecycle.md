---
title: "Azure Service Fabric Reliable Services 生命周期概述 | Microsoft 文档"
description: "了解 Service Fabric Reliable Services 中的不同生命周期事件"
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 3ff04f031b2d170ac63fc1c6f4e2b6ba6a6c46bd
ms.contentlocale: zh-cn
ms.lasthandoff: 08/19/2017

---

# <a name="reliable-services-lifecycle-overview"></a>Reliable Services 生命周期概述
> [!div class="op_single_selector"]
> * [Windows 上的 C#](service-fabric-reliable-services-lifecycle.md)
> * [Linux 上的 Java](service-fabric-reliable-services-lifecycle-java.md)
>
>

在考虑要 Reliable Services 的生命周期时，具备生命周期的基础知识这一点极为重要。 一般而言：

- 在启动期间
  - 构造服务
  - 服务可能会构造并返回零个或多个侦听器
  - 打开返回的所有侦听器，以便与服务通信
  - 调用服务的 RunAsync 方法，使服务能够执行长时间运行的工作或后台工作
- 在关闭期间
  - 取消传递给 RunAsync 的取消标记，同时关闭侦听器
  - 完成该过程后，销毁服务对象本身

有相应的文档详细介绍了有关这些事件的确切顺序。 具体而言，事件的顺序可能会根据 Reliable Service 是无状态服务还是有状态服务而略有变化。 此外，对于有状态服务，必须处理主副本交换方案。 在执行此序列期间，主副本的角色将转移到另一个副本（或者转移回来），而无需关闭服务。 最后，必须考虑到错误或失败状态。

## <a name="stateless-service-startup"></a>无状态服务启动
无状态服务的生命周期非常直接了当。 下面是事件的顺序：

1. 构造服务
2. 然后，并行发生两个事件：
    - 调用 `StatelessService.CreateServiceInstanceListeners()`，打开返回的所有侦听器。 对每个侦听器调用 `ICommunicationListener.OpenAsync()`
    - 调用服务的 `StatelessService.RunAsync()` 方法
3. 如果存在，则调用服务的 `StatelessService.OnOpenAsync()` 方法。 这是一种不常见的重写，但这种调用是可行的。

必须注意，在创建和打开侦听器与 RunAsync 时执行的调用之间没有一定的顺序。 可在启动 RunAsync 之前打开侦听器。 同样，在通信侦听器打开甚至构造之前，即可结束调用 RunAsync。 如果需要进行任何同步，这是实现器的任务。 常见解决方法：

  - 有时，在创建其他某些信息或者完成工作之前，侦听器无法正常运行。 对于无状态服务，该操作通常可在其他位置完成，例如： 
    - 在服务的构造函数中
    - 在 `CreateServiceInstanceListeners()` 调用期间
    - 作为侦听器本身构造的一部分
  - 有时，在侦听器打开之前，RunAsync 中的代码不希望启动。 在这种情况下，必须进行更多的协调。 一个常见的解决方案是在侦听器内使用某些标志指示它们完成的时间。 此标志签入 RunAsync，然后继续执行实际工作。

## <a name="stateless-service-shutdown"></a>无状态服务关闭
关闭无状态服务，将遵循相同的模式，只不过遵循的顺序相反：

1. 并行
    - 关闭任何打开的侦听器。 对每个侦听器调用 `ICommunicationListener.CloseAsync()`。
    - 取消传递给 `RunAsync()` 的取消令牌。 检查取消令牌的 `IsCancellationRequested` 属性是否返回 true，如果调用令牌的 `ThrowIfCancellationRequested` 方法，则会引发 `OperationCanceledException`。
2. 如果存在，针对每个侦听器完成 `CloseAsync()` 并且完成 `RunAsync()` 后，将调用服务的 `StatelessService.OnCloseAsync()` 方法。 重写 `StatelessService.OnCloseAsync()` 不常见。
3. 完成 `StatelessService.OnCloseAsync()` 后，销毁服务对象

## <a name="stateful-service-startup"></a>有状态服务启动
有状态服务的模式与无状态服务类似，只是稍有不同。 启动有状态服务时，事件的顺序如下所述：

1. 构造服务
2. 调用 `StatefulServiceBase.OnOpenAsync()`。 这是服务中不常见的重写。
3. 以下情况将并行发生
    - 调用 `StatefulServiceBase.CreateServiceReplicaListeners()` 
      - 如果服务是主副本，则打开所有返回的侦听器。 对每个侦听器调用 `ICommunicationListener.OpenAsync()`。
      - 如果服务为次要副本，将仅打开已标记为 `ListenOnSecondary = true` 的侦听器。 在次要副本上打开的侦听器更不常见。
    - 如果该服务目前为主副本，则调用该服务的 `StatefulServiceBase.RunAsync()` 方法
4. 所有副本侦听器的 `OpenAsync()` 调用完成并已调用 `RunAsync()` 后，将调用 `StatefulServiceBase.OnChangeRoleAsync()`。 这是服务中不常见的重写。

类似于无状态服务，创建和打开侦听器的顺序以及调用 RunAsync 的时间不会经过协调。 如果需要协调，解决方法大致相同。 另举一例：假设抵达通信侦听器的调用需要在某个 [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) 中保存信息。 由于通信侦听器可能在 Reliable Collections 可读或可写之前打开，因此，在 RunAsync 可以启动之前，必须经过一定的附加协调。 最简单且最常见的解决方法是让通信侦听器返回某个错误代码，告知客户端重试请求。

## <a name="stateful-service-shutdown"></a>有状态服务关闭
类似于无状态服务，关闭期间的生命周期事件与启动期间是相同的，但顺序相反。 关闭有状态服务时，将发生以下事件：

1. 并行
    - 关闭任何打开的侦听器。 对每个侦听器调用 `ICommunicationListener.CloseAsync()`。
    - 取消传递给 `RunAsync()` 的取消令牌。 检查取消令牌的 `IsCancellationRequested` 属性是否返回 true，如果调用令牌的 `ThrowIfCancellationRequested` 方法，则会引发 `OperationCanceledException`。
2. 针对每个侦听器完成 `CloseAsync()` 并且完成 `RunAsync()` 后，将调用服务的 `StatefulServiceBase.OnChangeRoleAsync()`。 （这是服务中不常见的重写。）
    - 只有当此服务副本是主副本时才需要等待 RunAsync 完成。
3. `StatefulServiceBase.OnChangeRoleAsync()` 方法完成后，将调用 `StatefulServiceBase.OnCloseAsync()` 方法。 这是一种不常见的重写，但这种调用是可行的。
3. 完成 `StatefulServiceBase.OnCloseAsync()` 后，销毁服务对象。

## <a name="stateful-service-primary-swaps"></a>有状态服务主副本交换
运行有状态服务时，只有该有状态服务的主副本打开其通信侦听器并调用其 RunAsync 方法。 会构造辅助副本，但不会对其执行进一步的调用。 但是，在运行有状态服务时当前用作主副本的副本可能会更改。 从副本看到的生命周期事件角度看，这意味着什么呢？ 有状态副本看到的行为取决于在交换期间该副本是已降级还是已升级。

### <a name="for-the-primary-being-demoted"></a>对于降级的主副本
Service Fabric 需要使用此副本来停止处理消息，退出正在执行的任何后台工作。 因此，此步骤类似于关闭服务时的情况。 一个差别在于，在此情况下不会销毁或关闭服务，因为它保留为辅助副本。 将调用以下 API：

1. 并行
    - 关闭任何打开的侦听器。 对每个侦听器调用 `ICommunicationListener.CloseAsync()`。
    - 取消传递给 `RunAsync()` 的取消令牌。 检查取消令牌的 `IsCancellationRequested` 属性是否返回 true，如果调用令牌的 `ThrowIfCancellationRequested` 方法，则会引发 `OperationCanceledException`。
2. 针对每个侦听器完成 `CloseAsync()` 并且完成 `RunAsync()` 后，将调用服务的 `StatefulServiceBase.OnChangeRoleAsync()`。 这是服务中不常见的重写。

### <a name="for-the-secondary-being-promoted"></a>对于升级的辅助副本
同样，Service Fabric 需要使用此副本来开始侦听网络上的消息，启动它所关注的任何后台任务。 因此，此过程类似于创建服务时的情况，只不过副本本身已存在。 将调用以下 API：

1. 并行
    - 调用 `StatefulServiceBase.CreateServiceReplicaListeners()`，打开返回的所有侦听器。 对每个侦听器调用 `ICommunicationListener.OpenAsync()`。
    - 调用服务的 `StatefulServiceBase.RunAsync()` 方法
2. 所有副本侦听器的 `OpenAsync()` 调用完成并已调用 `RunAsync()` 后，将调用 `StatefulServiceBase.OnChangeRoleAsync()`。 这是服务中不常见的重写。

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>有状态服务关闭和主副本降级期间的常见问题
Service Fabric 更改有状态服务的主副本的原因有多种。 最常见的原因是[群集重新平衡](service-fabric-cluster-resource-manager-balancing.md)和[应用程序升级](service-fabric-application-upgrade.md)。 在这些操作的过程中（以及在普通服务关闭过程中，比如你会了解是否已删除服务），服务必须遵从 `CancellationToken`。 如果服务不完全处理取消，会导致若干问题。 具体而言，因为 Service Fabric 等待服务正常停止而导致这些操作变慢。 最终导致升级超时失败，然后回滚。 未能遵循取消令牌也会导致群集不平衡，因为节点会变热，但无法重新平衡服务，因将它们移到其他位置耗时过长。 

由于服务有状态，它们也可能使用 [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)。 在 Service Fabric 中，主副本降级后，首先会撤销基础状态的写入访问权限。 这将导致可能影响服务生命周期的另一组问题。 集合将根据计时和是否已移动或关闭副本返回异常。 应正确处理这些异常。 由 Service Fabric 引发的异常分为永久性 [(`FabricException`)](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) 和暂时性 [(`FabricTransientException`)](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) 类别。 永久性异常应记录并引发，而暂时性异常可以基于某种重试逻辑重试。

处理因结合使用 `ReliableCollections` 和服务生命周期事件而产生的异常是测试和验证 Reliable Service 的重要环节。 建议在执行升级和[混沌测试](service-fabric-controlled-chaos.md)时始终低负载运行服务，然后再部署到生产环境。 以下基本步骤有助于确保已正确实现服务并正确处理生命周期事件。


## <a name="notes-on-service-lifecycle"></a>有关服务生命周期的说明
  - `RunAsync()` 方法和 `CreateServiceReplicaListeners/CreateServiceInstanceListeners` 调用都是可选的。 服务可能使用其中的一个或两个，或者都不使用。 例如，如果服务执行的所有工作都只是为了响应用户调用，则无需实现 `RunAsync()`。 只需提供通信侦听器及其关联的代码。 同样，创建和返回通信侦听器的操作也是可选的，因为服务可能只需执行后台工作，在这种情况下，只需实现 `RunAsync()`
  - 服务成功完成 `RunAsync()` 并从中返回即可。 完成不是失败条件。 完成 `RunAsync()` 表示该服务的后台工作已完成。 对于有状态可靠服务，如果副本已从主副本降级为次要副本，然后重新升级为主副本，则会再次调用 `RunAsync()`。
  - 如果服务因引发某种意外的异常从 `RunAsync()` 退出，将导致失败。 已关闭服务对象，并已报告运行状况错误。
  - 虽然从这些方法返回没有时间限制，但会立即丧失写入到可靠集合的能力，并因此无法完成任何实际工作。 建议尽快在收到取消请求后返回。 如果服务在合理的时间内未响应这些 API 调用，Service Fabric 可能会强行终止服务。 通常，只有在应用程序升级期间或删除服务时，才发生这种情况。 此超时默认为 15 分钟。
  - `OnCloseAsync()` 路径中的失败会导致调用 `OnAbort()`，服务可以凭借这最后一个机会，尽最大努力清理并释放它们所占用的资源。

## <a name="next-steps"></a>后续步骤
- [Reliable Services 简介](service-fabric-reliable-services-introduction.md)
- [Reliable Services 快速启动](service-fabric-reliable-services-quick-start.md)
- [Reliable Services 高级用法](service-fabric-reliable-services-advanced-usage.md)

