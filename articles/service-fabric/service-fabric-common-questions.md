---
title: "有关 Microsoft Azure Service Fabric 的常见问题 | Microsoft 文档"
description: "下面是有关 Service Fabric 的一些常见问题及其解答："
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/19/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 102be620e8812cc551aebafe7c8df4e4eac0ae90
ms.openlocfilehash: 2ad3bd7b846693c637fd843383802651a619b128

---


# <a name="commonly-asked-service-fabric-questions"></a>有关 Service Fabric 的常见问题

在 Service Fabric 的作用及其用法方面，我们收集了大量的常见问题。 本文档包含其中的许多常见问题及其解答。

## <a name="cluster-setup-and-management"></a>群集设置和管理

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions"></a>是否可以创建跨越多个 Azure 区域的群集？

目前不行，但这是一个常见的请求，我们将继续展开调研。

核心 Service Fabric 群集技术并不区分 Azure 区域，可用于将世界各地运行的计算机集合到一起，前提是它们相互之间已建立网络连接。 但是，如同构建群集的虚拟机规模集一样，Azure 中的 Service Fabric 群集资源也是区域性的。 此外，在远距离分散的计算机之间提供非常一致的数据复制方面，还存在一个固有的难题。 在支持跨区域群集之前，我们希望确保性能可预测且可接受。

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Service Fabric 节点是否自动接收 OS 更新？

目前不是，但这也是一个常见的请求，我们有意推出此功能。

OS 更新的难题在于，它们通常需要重新启动计算机，而这会导致暂时失去可用性。 实现该请求本身不存在问题，因为 Service Fabric 会自动将这些服务的流量重定向到其他节点。 但是，如果不在群集之间协调 OS 更新，多个节点有可能会同时关闭。 这种同时重新启动可能会导致某个服务完全失去可用性，至少特定的分区会失去可用性（对于有状态服务）。

将来，我们将会支持在更新域之间完全自动化的、经过协调的 OS 更新策略，确保即使重新启动或发生其他意外故障，也仍能保持可用性。

在此期间，我们[提供脚本](https://blogs.msdn.microsoft.com/azureservicefabric/2017/01/09/os-patching-for-vms-running-service-fabric/)，群集管理器可使用该脚本，以安全的方式手动启动每个节点的修补程序。

### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Service Fabric 群集的最小大小是多少？ 为何不能更小一点？

运行生产工作负荷的 Service Fabric 群集支持的最小大小为五个节点。 对于开发/测试方案，我们支持三节点群集。

之所以规定这些最小大小，是因为 Service Fabric 群集要运行一组有状态系统服务，包括命名服务和故障转移管理器。 这些服务跟踪哪些服务已部署到群集及其当前的托管位置，取决于非常一致性。 而这种非常一致性又取决于能否获取*仲裁*来更新这些服务的状态，其中，仲裁表示给定服务在严格意义上的大多数副本 (N/2 +&1;)。

在了解这种背景的前提下，让我们探讨一些可能的群集配置：

**单节点**：此选项无法提供高可用性，因为出于任何原因丢失单个节点就意味着丢失整个群集。

**双节点**：跨两个节点 (N = 2) 部署的服务的仲裁为 2 (2/2 + 1 = 2)。 丢失单个副本时，无法创建仲裁。 由于执行服务升级要求暂时关闭副本，因此这不是一个有用的配置。

**三节点**：包含三个节点 (N = 3)，创建仲裁仍然要求使用两个节点 (3/2 + 1 = 2)。 这意味着，可以丢失单个节点，在这种情况下，仍可保留仲裁。

支持在开发/测试环境中使用三节点群集配置，因为可以安全执行升级，承受单个节点的故障，只要它们不同时发生故障。 对于生产工作负荷，必须能够弹性应对这种同时发生的故障，因此需要五个节点。

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>是否可以在夜间/周末关闭群集以节约成本？

一般来说是不可以的。 Service Fabric 在本地临时磁盘上存储状态，这意味着，如果将虚拟机转移到另一台主机，数据不会随着它一起移动。 在正常操作中，这是没有问题的，因为其他节点可使新节点保持最新状态。 但是，如果停止所有节点，然后又将它们重新启动，则很有可能大多数节点在新主机上启动，使得系统无法恢复。

如果在部署应用程序之前想要创建群集来测试应用程序，我们建议将这些群集动态创建为[持续集成/持续部署管道](service-fabric-set-up-continuous-integration.md)的一部分。

## <a name="application-design"></a>应用程序设计

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>跨 Reliable Collection 分区查询数据的最佳方法是什么？

Reliable Collections 通常已[分区](service-fabric-concepts-partitioning.md)以支持扩展并提高性能和吞吐量。 这意味着，给定服务的状态可以分散在数十甚至数百台计算机上。 要针对整个数据集执行操作，可采用以下几种做法：

- 创建一个服务用于查询另一服务的所有分区，提取所需的数据。
- 创建一个可从另一服务的所有分区接收数据的服务。
- 定期将每个服务中的数据推送到外部存储。 仅当执行的查询不是核心业务逻辑的一部分时，此方法才适用。


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>跨执行组件查询数据的最佳方法是什么？

执行组件在设计上是独立的状态和计算单元，因此不建议在运行时针对执行组件执行广泛查询。 如果需要跨整个执行组件状态集查询，应考虑：

- 将执行组件服务替换为有状态 Reliable Services，使得从一定数目的执行组件中收集所有数据的网络请求数等于服务中的分区数。
- 将执行组件设计为定期向外部存储推送其状态，以方便查询。 如前所述，仅当运行时行为不需要所执行的查询时，此方法才可行。

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>在 Reliable Collection 中可以存储多少数据？

Reliable Services 通常已分区，因此，可存储的数据量受到群集中计算机数目以及这些计算机上的可用内存量的限制。

例如，某个服务中的 Reliable Collection 包含 100 个分区和 3 个副本，存储平均大小为 1KB 的对象。 现在，假设创建了一个由 10 台计算机构成的群集，其中每台计算机有 16GB 内存。 我们做一个简单且保守的假设：操作系统和系统服务、Service Fabric 运行时以及服务消耗了 6GB 内存，也就是说，每个计算机还剩余 10GB 内存，整个群集还有 100GB 内存。

请记住，每个对象必须存储三次（一个主体和两个副本），因此，在运转整个容量时，需要为集合中大约 3500 万个对象留出足够的内存。 但是，我们建议能够弹性应对故障域和升级域的同时丢失（它们会占用大约 1/3 的容量），并将对象数目减少到大约 2300 万个。

请注意，这种计算还基于以下假设：

- 跨分区的数据分布大致是均匀的，或者你可以向群集资源管理器报告负载指标。 默认情况下，Service Fabric 会根据副本计数执行负载均衡。 在上述示例中，群集中的每个节点上将会放置 10 个主副本和 20 个辅助副本。 对于均匀分布在分区之间的负载而言，这是没有问题的。 如果负载不均衡，则必须报告负载，使资源管理器能够将较小的副本打包在一起，让较大的副本消耗单个节点上的更多内存。

- 只有在群集中存储状态的 Reliable Service 才受到影响。 由于可将多个服务部署到群集，因此需要注意每个服务所要运行的资源，并管理服务的状态。

- 群集本身不会扩大或缩小。 如果添加更多计算机，Service Fabric 会重新均衡副本来利用更多容量，直到计算机数目超过了服务中的分区数，因为单个副本不能跨计算机。 相比之下，如果通过删除计算机来减小群集大小，副本将更加严密地打包，减少总体容量消耗。

### <a name="how-much-data-can-i-store-in-an-actor"></a>在执行组件中可以存储多少数据？

与 Reliable Services 一样，在执行组件服务中可以存储的数据量仅受群集中节点上的总磁盘空间和可用内存量的限制。 但是，如果使用单个执行组件来封装少量的状态和关联的业务逻辑，则它们可以发挥最大的效率。 一般规则是，单个执行组件中应该包含以 KB 计量的状态数据。

## <a name="other-questions"></a>其他问题

### <a name="how-does-service-fabric-relate-to-containers"></a>Service Fabric 与容器之间存在怎样的关系？

使用容器可以方便地打包服务及其依赖项，以便这些服务在所有环境中以一致的方式运行，在单台计算机上以隔离的方式工作。 使用 Service Fabric 可以部署和管理服务，包括[在容器中打包的服务](service-fabric-containers-overview.md)。

### <a name="are-you-planning-to-open-source-service-fabric"></a>是否想要开放 Service Fabric 源代码？

我们想要在 GitHub 上开放 Reliable Services 和 Reliable Actors 框架的源代码并接受社区对这些项目的投稿。 有关已发布的更多详细信息，请参阅 [Service Fabric 博客](https://blogs.msdn.microsoft.com/azureservicefabric/)。

当前不打算开放 Service Fabric 运行时的源代码。

## <a name="next-steps"></a>后续步骤

- [了解核心 Service Fabric 概念和最佳实践](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)



<!--HONumber=Jan17_HO3-->


