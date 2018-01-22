---
title: "Azure Service Fabric Reliable Services 生命周期 | Microsoft Docs"
description: "了解 Service Fabric Reliable Services 中的生命周期事件"
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: ad4228ade68f4494e5be0454643752e742c1cc81
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2018
---
# <a name="reliable-services-lifecycle"></a>Reliable Services 生命周期
> [!div class="op_single_selector"]
> * [Windows 上的 C#](service-fabric-reliable-services-lifecycle.md)
> * [Linux 上的 Java](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services 是 Azure Service Fabric 中可用的编程模型之一。 了解 Reliable Services 的生命周期时，最重要的是要理解基本生命周期事件。 事件的确切顺序取决于配置详细信息。 

一般情况下，Reliable Services 生命周期包括以下事件：

* 在启动期间：
  * 构造服务。
  * 服务可能会构造并返回零个或多个侦听器。
  * 打开返回的任何侦听器，以便与服务通信。
  * 调用服务的 `runAsync` 方法，使服务能够执行长时间运行的工作或后台工作。
* 在关闭期间：
  * 取消传递给 `runAsync` 的取消令牌，同时关闭侦听器。
  * 销毁服务对象本身。

根据可靠服务是无状态服务还是有状态服务，Reliable Services 中事件的顺序可能略有变化。 

此外，对于有状态服务，必须处理主副本交换方案。 在执行此序列期间，主副本的角色将转移到另一个副本（或者转移回来），而无需关闭服务。 

最后，必须考虑错误或失败条件。

## <a name="stateless-service-startup"></a>无状态服务启动
无状态服务的生命周期非常直接了当。 下面是事件的顺序：

1. 构造服务。
2. 这些事件将并行发生：
    - 调用 `StatelessService.createServiceInstanceListeners()`，打开返回的所有侦听器。 对每个侦听器调用 `CommunicationListener.openAsync()`。
    - 调用服务的 `runAsync` 方法 (`StatelessService.runAsync()`)。
3. 如果存在，则调用服务本身的 `onOpenAsync` 方法。 具体而言，即调用 `StatelessService.onOpenAsync()`。 这是一种不常见的重写，但这种调用是可行的。

必须注意，用于创建和打开侦听器的调用和 `runAsync` 调用之间没有一定的顺序。 侦听器可能在 `runAsync` 启动前就已打开。 同样，在通信侦听器打开甚至构造之前，即可调用 `runAsync`。 如果需要进行任何同步，它必须由实现器完成。 下面是一些常见的解决方案：

* 有时，在创建其他信息或完成其他工作之前，侦听器无法正常运行。 对于无状态服务，通常可在服务的构造函数中完成该工作。 可以在 `createServiceInstanceListeners()` 调用过程中完成，也可在构造侦听器本身的过程中完成。
* 有时，在侦听器打开之前，`runAsync` 中的代码不会启动。 在这种情况下，必须进行更多的协调。 常见解决方案是向侦听器中添加标志。 用标志指示侦听器完成的时间。 `runAsync` 方法将检查此时间再继续进行实际工作。

## <a name="stateless-service-shutdown"></a>无状态服务关闭
关闭无状态服务，将遵循相同的模式，但顺序相反：

1. 这些事件将并行发生：
    - 关闭任何打开的侦听器。 对每个侦听器调用 `CommunicationListener.closeAsync()`。
    - 取消传递给 `runAsync()` 的取消令牌。 检查取消令牌的 `isCancelled` 属性是否返回 `true`，如果已调用，则令牌的 `throwIfCancellationRequested` 方法会引发 `CancellationException`。
2. 当 `closeAsync()` 每个侦听器完成，且 `runAsync()` 也完成后，调用服务的 `StatelessService.onCloseAsync()` 方法（如果存在）。 再次强调，这不是常用替代。
3. 完成 `StatelessService.onCloseAsync()` 后，销毁服务对象。

## <a name="stateful-service-startup"></a>有状态服务启动
有状态服务的模式与无状态服务类似，只是稍有不同。 启动有状态服务时，事件的顺序如下：

1. 构造服务。
2. 调用 `StatefulServiceBase.onOpenAsync()`。 此调用是服务中不常见的重写。
3. 这些事件将并行发生：
    - 调用 `StatefulServiceBase.createServiceReplicaListeners()`。 
      - 如果服务是主要服务，则打开所有返回的侦听器。 对每个侦听器调用 `CommunicationListener.openAsync()`。
      - 如果服务是辅助服务，则只打开已标记为 `listenOnSecondary = true` 的侦听器。 在辅助服务上打开的侦听器较不常见。
    - 如果该服务目前是主要服务，则调用该服务的 `StatefulServiceBase.runAsync()` 方法。
4. 所有副本侦听器的 `openAsync()` 调用完成并已调用 `runAsync()` 后，将调用 `StatefulServiceBase.onChangeRoleAsync()`。 此调用是服务中不常见的重写。

类似于无状态服务，有状态服务中也不会协调创建和打开侦听器的顺序以及调用 `runAsync` 的时间。 如果需要协调，解决方法大致相同。 对于有状态服务，还存在一种情况。 假设抵达通信侦听器的调用需要在某个 [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) 中保存信息。 由于通信侦听器可能在 Reliable Collections 可读或可写之前打开，因此在 `runAsync` 启动之前，必须经过额外协调。 最简单且最常见的解决方法是让通信侦听器返回错误代码。 客户端将通过该错误代码了解要重试请求。

## <a name="stateful-service-shutdown"></a>有状态服务关闭
与无状态服务一样，关闭期间的生命周期事件与启动期间是相同的，但顺序相反。 关闭有状态服务时，将发生以下事件：

1. 这些事件将并行发生：
    - 关闭任何打开的侦听器。 对每个侦听器调用 `CommunicationListener.closeAsync()`。
    - 取消传递给 `runAsync()` 的取消令牌。 调用取消令牌的 `isCancelled()` 属性返回 `true`，如果已调用，则令牌的 `throwIfCancellationRequested()` 方法会引发 `OperationCanceledException`。
2. 在针对每个侦听器完成 `closeAsync()` 并且完成 `runAsync()` 后，调用服务的 `StatefulServiceBase.onChangeRoleAsync()`。 此调用是服务中不常见的重写。

   > [!NOTE]  
   > 仅当此副本是主副本时，才需要等待 `runAsync` 完成。

3. `StatefulServiceBase.onChangeRoleAsync()` 方法完成后，调用 `StatefulServiceBase.onCloseAsync()` 方法。 此调用是一种不常见的重写，但是可行的。
3. 完成 `StatefulServiceBase.onCloseAsync()` 后，销毁服务对象。

## <a name="stateful-service-primary-swaps"></a>有状态服务主副本交换
运行有状态服务时，只对该有状态服务的主副本打开通信侦听器并调用 `runAsync` 方法。 会构造辅助副本，但不会对其执行进一步的调用。 在运行有状态服务时，当前用作主副本的副本可能会更改。 对有状态副本可见的生命周期事件取决于该副本在交换期是降级还是升级了。

### <a name="for-the-demoted-primary"></a>对于降级的主副本
Service Fabric 需要使用降级的主副本，以停止处理消息，并停止任何后台工作。 此步骤与关闭服务时类似。 一个差别在于，在此情况下不会销毁或关闭服务，因为它保留为次要副本。 将发生以下事件：

1. 这些事件将并行发生：
    - 关闭任何打开的侦听器。 对每个侦听器调用 `CommunicationListener.closeAsync()`。
    - 取消传递给 `runAsync()` 的取消令牌。 检查取消标记的 `isCancelled()` 方法是否返回 `true`。 如果已调用，则令牌的 `throwIfCancellationRequested()` 方法将引发 `OperationCanceledException`。
2. 在针对每个侦听器完成 `closeAsync()` 并且完成 `runAsync()` 后，调用服务的 `StatefulServiceBase.onChangeRoleAsync()`。 此调用是服务中不常见的重写。

### <a name="for-the-promoted-secondary"></a>对于升级的次要副本
同样，Service Fabric 需要提升的次要副本，才能开始侦听网络上的消息，并启动需要完成的任何后台任务。 此过程与创建服务时类似。 不同之处在于副本本身已存在。 将发生以下事件：

1. 这些事件将并行发生：
    - 调用 `StatefulServiceBase.createServiceReplicaListeners()`，打开返回的所有侦听器。 对每个侦听器调用 `CommunicationListener.openAsync()`。
    - 调用服务的 `StatefulServiceBase.runAsync()` 方法。
2. 所有副本侦听器的 `openAsync()` 调用完成并已调用 `runAsync()` 后，将调用 `StatefulServiceBase.onChangeRoleAsync()`。 此调用是服务中不常见的重写。

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>有状态服务关闭和主副本降级期间的常见问题
Service Fabric 更改有状态服务的主副本的原因有多种。 最常见的原因是[群集重新均衡](service-fabric-cluster-resource-manager-balancing.md)和[应用程序升级](service-fabric-application-upgrade.md)。 这些在操作期间，请务必使服务遵循 `cancellationToken`。 这在正常服务在关闭期间也适用，比如删除服务时。

如果服务不完全处理取消，可能会导致若干问题。 这些操作的速度之所以缓慢，是因为 Service Fabric 要等待服务正常停止。 最终导致升级超时失败，然后回滚。 未能遵循取消令牌也可能导致群集不均衡。 群集将由于节点变热而不均衡。 但是，将它们移到其他位置耗时过长，因此无法重新均衡服务。 

由于服务有状态，所以它们也可能使用 [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)。 在 Service Fabric 中，主副本降级后，首先会撤销基础状态的写入访问权限。 这会导致可能影响服务生命周期的另外一系列问题。 集合将根据计时和是否已移动或关闭副本返回异常。 请务必正确处理这些异常。 

由 Service Fabric 引发的异常可能是永久的 [(`FabricException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception) 或临时的 [(`FabricTransientException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception._fabric_transient_exception)。 应记录并引发永久异常。 可以基于重试逻辑重试临时异常。

测试和验证 Reliable Services 时，处理因结合使用 `ReliableCollections` 和服务生命周期事件而产生的异常是一个重要环节。 建议始终在负载范围内运行服务。 还应执行升级和[混沌测试](service-fabric-controlled-chaos.md)，然后再部署到生产环境。 以下基本步骤有助于确保已正确实现服务和处理生命周期事件。

## <a name="notes-on-service-lifecycle"></a>有关服务生命周期的说明
* `runAsync()` 方法和 `createServiceInstanceListeners/createServiceReplicaListeners` 调用都是可选的。 一项服务可能符合其中一项、两项或均不符合。 例如，如果服务执行的所有工作都只是为了响应用户调用，则无需实现 `runAsync()`。 只需提供通信侦听器及其关联的代码。 

  同样，创建和返回通信侦听器是可选的。 该服务可能具有仅后台工作要做，因此它只需实现 `runAsync()`。
* 服务成功完成 `runAsync()` 并从中返回即可。 这不会被视为失败条件。 它表示服务后台工作的完成。 对于有状态可靠服务，如果服务已从主副本降级，然后重新升级为主副本，则会再次调用 `runAsync()`。
* 如果服务因引发某种意外的异常从 `runAsync()` 退出，将导致失败。 已关闭服务对象，并已报告运行状况错误。
* 虽然从这些方法返回没有时间限制，但会立即丧失写入的能力。 因此，无法完成任何实际工作。 建议尽快在收到取消请求后返回。 如果服务在合理的时间内未响应这些 API 调用，Service Fabric 可能会强行终止服务。 通常，只有在应用程序升级期间或删除服务时，才发生这种情况。 此超时默认为 15 分钟。
* `onCloseAsync()` 路径中的故障将导致 `onAbort()` 调用。 这一调用是最后一个机会，服务会尽最大努力清理并释放其占用的资源。

## <a name="next-steps"></a>后续步骤
* [Reliable Services 简介](service-fabric-reliable-services-introduction.md)
* [Reliable Services 快速启动](service-fabric-reliable-services-quick-start-java.md)
* [Reliable Services 高级用法](service-fabric-reliable-services-advanced-usage.md)
