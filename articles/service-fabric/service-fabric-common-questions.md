---
title: 有关 Microsoft Azure Service Fabric 的常见问题 | Microsoft 文档
description: 下面是有关 Service Fabric 的一些常见问题及其解答：
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: a40432aa1d9a466706b4a3ebbcbd56cd8e5b768e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="commonly-asked-service-fabric-questions"></a>有关 Service Fabric 的常见问题

在 Service Fabric 的作用及其用法方面，我们收集了大量的常见问题。 本文档包含其中的许多常见问题及其解答。

## <a name="cluster-setup-and-management"></a>群集设置和管理

### <a name="how-do-i-rollback-my-service-fabric-cluster-certificate"></a>如何回退 Service Fabric 群集证书？

回退应用程序的任何升级需要在提交更改的 Service Fabric 群集仲裁前，进行运行状况故障检测；已提交的更改只能前滚。 如果引入了不受监控的重大证书更改，则可能需要呈报工程师的通过客户支持服务才能恢复群集。  [Service Fabric 的应用程序升级](https://review.docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-upgrade?branch=master)应用[应用程序升级参数](https://review.docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)，并提供零停机时间升级承诺。  按照建议的应用程序升级监视模式，更新域上的自动更新进度基于运行状况检查是否通过，如果更新默认服务失败，将自动回退。
 
如果你的群集仍在利用资源管理器模板中的经典 Certificate Thumbprint 属性，建议你[将群集从证书指纹更改为公用名称](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn)，以便利用新式机密管理功能。

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>是否可以创建跨越多个 Azure 区域或自己的数据中心的群集？

是的。 

核心 Service Fabric 群集技术可用于将世界各地运行的计算机集合到一起，前提是它们相互之间已建立网络连接。 然而，生成并运行这样的群集可能很复杂。

如果你对此方案有兴趣，建议你通过 [Service Fabric Github 问题列表](https://github.com/azure/service-fabric-issues)或通过支持代表联系我们以获取其他指南。 Service Fabric 团队正在努力针对此方案提供其他解释、指南和建议。 

应考虑的一些事项： 

1. 如同构建群集的虚拟机规模集一样，Azure 中的 Service Fabric 群集资源如今也是区域性的。 这意味着如果出现区域性故障，将可能无法通过 Azure 资源管理器或 Azure 门户管理群集。 即使群集仍在运行，且你能够直接与其交互，也可能发生此情况。 此外，Azure 目前不提供创建跨区域可用的单个虚拟网络的功能。 这意味着 Azure 中的多区域群集需要[适用于 VM 规模集中的每个 VM 的公共 IP 地址](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine)或 [Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 这些网络选择对成本、性能以及某种程度上的应用程序设计都有不同的影响，因此需要在选择此类环境前仔细分析和规划。
2. 维护、管理和监视这些计算机可能会变得很复杂，尤其是在跨多种类型环境时，比如不同云提供程序之间或本地资源和 Azure 之间。 必须格外小心，确保在此类环境中运行生产工作负荷前，已了解群集和应用程序的升级、监视、管理和诊断。 如果你已有在 Azure 或自己的数据中心解决这些问题的经验，则很可能这些相同的解决方案在生成或运行 Service Fabric 群集时均适用。 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Service Fabric 节点是否自动接收 OS 更新？

目前不是，但这也是一个常见的请求，Azure 有意推出此功能。

在此期间，我们已[提供一个应用程序](service-fabric-patch-orchestration-application.md)，Service Fabric 节点下的操作系统不断修补并保持最新状态。

OS 更新的难题在于，它们通常需要重新启动计算机，而这会导致暂时失去可用性。 实现该请求本身不存在问题，因为 Service Fabric 会自动将这些服务的流量重定向到其他节点。 但是，如果不在群集之间协调 OS 更新，多个节点有可能会同时关闭。 这种同时重新启动可能会导致某个服务完全失去可用性，至少特定的分区会失去可用性（对于有状态服务）。

将来，我们计划支持在更新域之间完全自动化的、经过协调的 OS 更新策略，确保即使重新启动或发生其他意外故障，也仍能保持可用性。

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>是否可以在我的 SF 群集中使用大型虚拟机规模集？ 

**简短解答** - 否。 

**详细解答** - 尽管通过大型虚拟机规模集可将虚拟机规模集扩展至多达 1000 个 VM 实例，但这是通过使用放置组 (PG) 实现的。 容错域 (FD) 和升级域 (UD) 仅在使用 FD 和 UD 来为服务副本/服务实例做出放置决策的放置组 Service Fabric 中保持一致。 因为 FD 和 UD 仅在放置组中可比较，因此 SF 无法使用它。 例如，如果 PG1 中的 VM1 具有一个 FD=0 的拓扑，并且 PG2 中的 VM9 具有一个 FD=4 的拓扑，这并不意味着 VM1 和 VM2 在两个不同的硬件机架上，因此在这种情况下 SF 无法使用 FD 值做出放置决策。

当前，大型虚拟机规模集还存在其他问题，例如缺少 level-4 负载均衡支持。 请参考[有关大型规模集的详细信息](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Service Fabric 群集的最小大小是多少？ 为何不能更小一点？

运行生产工作负荷的 Service Fabric 群集支持的最小大小为五个节点。 对于开发/测试方案，我们支持三节点群集。

之所以规定这些最小大小，是因为 Service Fabric 群集要运行一组有状态系统服务，包括命名服务和故障转移管理器。 这些服务跟踪哪些服务已部署到群集及其当前的托管位置，取决于非常一致性。 而这种非常一致性又取决于能否获取*仲裁*来更新这些服务的状态，其中，仲裁表示给定服务在严格意义上的大多数副本 (N/2 + 1)。

在了解这种背景的前提下，让我们探讨一些可能的群集配置：

**单节点**：此选项无法提供高可用性，因为出于任何原因丢失单个节点就意味着丢失整个群集。

**双节点**：跨两个节点 (N = 2) 部署的服务的仲裁为 2 (2/2 + 1 = 2)。 丢失单个副本时，无法创建仲裁。 由于执行服务升级要求暂时关闭副本，因此这不是一个有用的配置。

**三节点**：包含三个节点 (N = 3)，创建仲裁仍然要求使用两个节点 (3/2 + 1 = 2)。 这意味着，可以丢失单个节点，在这种情况下，仍可保留仲裁。

支持在开发/测试环境中使用三节点群集配置，因为可以安全执行升级，承受单个节点的故障，只要它们不同时发生故障。 对于生产工作负荷，必须能够弹性应对这种同时发生的故障，因此需要五个节点。

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>是否可以在夜间/周末关闭群集以节约成本？

一般来说是不可以的。 Service Fabric 在本地临时磁盘上存储状态，这意味着，如果将虚拟机转移到另一台主机，数据不会随着它一起移动。 在正常操作中，这是没有问题的，因为其他节点可使新节点保持最新状态。 但是，如果停止所有节点，又将它们重新启动，则很有可能大多数节点在新主机上启动，使得系统无法恢复。

如果在部署应用程序之前想要创建群集来测试应用程序，我们建议将这些群集动态创建为[持续集成/持续部署管道](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)的一部分。


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>如何升级操作系统（例如从 Windows Server 2012 升级到 Windows Server 2016）？

我们致力于改善体验，但现在升级由你负责。 必须升级群集虚拟机上的操作系统映像，一次升级一个 VM。 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>是否可以对群集节点类型（虚拟机规模集）中的附加数据磁盘进行加密？
是的。  有关详细信息，请参阅[创建具有附加数据磁盘的群集](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks)、[加密磁盘 (PowerShell)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) 和[加密磁盘 (CLI)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md)。

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>在群集中运行防病毒程序时需要排除哪些目录和进程？

| **防病毒排除的目录** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot（从群集配置中） |
| FabricLogRoot（从群集配置中） |

| **防病毒排除的进程** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
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

例如，某个服务中的 Reliable Collection 包含 100 个分区和 3 个副本，存储平均大小为 1 KB 的对象。 现在，假设创建了一个由 10 台计算机构成的群集，其中每台计算机有 16GB 内存。 我们做一个简单且保守的假设：操作系统和系统服务、Service Fabric 运行时以及服务消耗了 6GB 内存，也就是说，每个计算机还剩余 10GB 内存，整个群集还有 100GB 内存。

请记住，每个对象必须存储三次（一个主体和两个副本），因此，在运转整个容量时，需要为集合中大约 3500 万个对象留出足够的内存。 但是，我们建议能够弹性应对故障域和升级域的同时丢失（它们会占用大约 1/3 的容量），并将对象数目减少到大约 2300 万个。

请注意，这种计算还基于以下假设：

- 跨分区的数据分布大致是均匀的，或者可向群集资源管理器报告负载指标。 默认情况下，Service Fabric 会根据副本计数执行负载均衡。 在前面的示例中，群集中的每个节点上会放置 10 个主副本和 20 个辅助副本。 对于均匀分布在分区之间的负载而言，这是没有问题的。 如果负载不均衡，必须报告负载，使 Resource Manager 能够将较小副本打包在一起，增加较大副本在单个节点上占用的内存。

- 只有在群集中存储状态的 Reliable Service 才受到影响。 由于可将多个服务部署到群集，因此你需要知道每个服务运行时以及管理其状态时所需的资源。

- 群集本身不会扩大或缩小。 如果添加更多计算机，Service Fabric 会重新均衡副本来利用更多容量，直到计算机数目超过了服务中的分区数，因为单个副本不能跨计算机。 相比之下，如果通过删除计算机来减小群集大小，副本将更加严密地打包，减少总体容量消耗。

### <a name="how-much-data-can-i-store-in-an-actor"></a>在执行组件中可以存储多少数据？

与 Reliable Services 一样，在执行组件服务中可以存储的数据量仅受群集中节点上的总磁盘空间和可用内存量的限制。 但是，如果使用单个执行组件来封装少量的状态和关联的业务逻辑，则它们可以发挥最大的效率。 一般规则是，单个执行组件中应该包含以 KB 计量的状态数据。

## <a name="other-questions"></a>其他问题

### <a name="how-does-service-fabric-relate-to-containers"></a>Service Fabric 与容器之间存在怎样的关系？

使用容器可以方便地打包服务及其依赖项，以便这些服务在所有环境中以一致的方式运行，在单台计算机上以隔离的方式工作。 使用 Service Fabric 可以部署和管理服务，包括[在容器中打包的服务](service-fabric-containers-overview.md)。

### <a name="are-you-planning-to-open-source-service-fabric"></a>你们是否打算开放 Service Fabric 源代码？

我们已在 GitHub 上开放了部分 Service Fabric 源代码（[可靠服务框架](https://github.com/Azure/service-fabric-services-and-actors-dotnet)、[可靠执行组件框架](https://github.com/Azure/service-fabric-services-and-actors-dotnet)、[ASP.NET Core 集成库](https://github.com/Azure/service-fabric-aspnetcore)、[Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer) 和 [Service Fabric CLI](https://github.com/Azure/service-fabric-cli)），并接受有关这些项目的社区投稿。 

我们[最近宣布了](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/)计划完全开放 Service Fabric 运行时源代码。 当前，[Service Fabric 存储库](https://github.com/Microsoft/service-fabric/)可在 GitHub 与 Linux 生成和测试工具上运行，这意味着可克隆存储库、为 Linux 构建 Service Fabric、运行基本测试、提出问题并提交拉取请求。 我们正在努力将 Windows 生成环境以及完整的 CI 环境迁移过来。

有关已发布的更多详细信息，请参阅 [Service Fabric 博客](https://blogs.msdn.microsoft.com/azureservicefabric/)。

## <a name="next-steps"></a>后续步骤

- [了解核心 Service Fabric 概念和最佳实践](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)
