<properties
   pageTitle="某些 Azure Service Fabric Actor 反模式"
   description="适用于要了解 Azure Service Fabric Actor 的客户的一些潜在缺陷"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/11/2015"
   wacn.date=""/>

# Reliable Actors 设计模式：若干反模式

我们为了解 Service Fabric Reliable Actors 的客户标识以下潜在的缺陷：

* 将 Reliable Actors 视为事务系统。Service Fabric Reliable Actors 不是提供 ACID 的基于两阶段提交的系统。如果我们未实现可选的持久性，并且 Actor 正在运行的计算机停止运行，其当前状态会随之停止。Actor 会非常快地提出另一个节点，但除非我们已经实现备份持久性，否则状态将被删除。但是，在利用重试、重复筛选和/或幂等设计之间，你可以获得高级别的可靠性和一致性。

* 阻止。我们在 Reliable Actors 中执行的所有操作都应为异步操作。这通常很容易，因为异步 API 现在在 Microsoft 平台中很多见。但如果出于某种原因，我们必须与仅提供阻止 API 的系统进行交互，我们将需要将之放入显式使用.NET 线程池的包装器。

* 通过架构师。让环境起作用。对于习惯于担心并发集合和锁或使用工具编译 XML 中的对象的开发人员而言，只对执行简单事项（例如将值分配给变量或计划的工作）的类编码可能很困难。计划的任务是内置的。不需要锁。状态不是严重的防范对象。对于在大规模环境中执行过大量服务器端工作的很多人而言，这需要一段时间才能习惯。

* 使单个 Actor 成为瓶颈。通常很容易困在此瓶颈中，数以百万计的 Actor 通过渠道成为另一个 Actor 的单个实例。使用我们在[分布式计算设计模式](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-computation)中所示的聚合方法。

* 盲目地映射实体模型。这适用于来自使用实体及其关系来模拟问题的关系领域的开发人员。这种方法仍可用于了解主题域，它应与面向服务的思维结合并与行为混合。

<!---HONumber=74-->