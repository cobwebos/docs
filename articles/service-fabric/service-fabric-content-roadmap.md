---
title: 详细了解 Azure Service Fabric
description: 了解 Azure Service Fabric 的核心概念和主要应用领域。 扩展概述了 Service Fabric 以及如何创建微服务。
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 573b1ec662bdc7e72f964698f5e0670860895586
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791844"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>想要了解 Service Fabric 吗？
Service Fabric 是分布式系统平台，可借助它轻松打包、部署和管理可缩放且可靠的微服务。  不过，Service Fabric 的外围应用领域广泛，有很多东西需要学习。  本文简要说明了 Service Fabric，并介绍了核心概念、编程模型、应用程序生命周期、测试、群集和运行状况监视。 请参阅[概述](service-fabric-overview.md)和[什么是微服务？](service-fabric-overview-microservices.md)，概览相关信息，并了解如何使用 Service Fabric 创建微服务。 本文包含的内容列表虽不完整，但确实提供了 Service Fabric 每个应用领域的概述和入门文章链接。 

## <a name="core-concepts"></a>核心概念
此处介绍基础知识，有关详细概念和介绍，可参阅 [Service Fabric 术语](service-fabric-technical-overview.md)、[应用程序模型](service-fabric-application-model.md)和[支持的编程模型](service-fabric-choose-framework.md)。

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>设计时：服务类型、服务包和清单，应用程序类型、应用程序包和清单
服务类型是分配给服务的代码包、数据包、配置包的名称/版本。 此项在 ServiceManifest.xml 文件中定义。 服务类型由在运行时加载的可执行代码和服务配置设置以及服务使用的静态数据组成。

服务包是包含服务类型的 ServiceManifest.xml 文件的磁盘目录，引用服务类型的代码、静态数据和配置包。 例如，服务包可能引用构成数据库服务的代码、静态数据和配置包。

应用程序类型是分配给服务类型集合的名称/版本。 此项在 ApplicationManifest.xml 文件中定义。

![Service Fabric 应用程序类型和服务类型][cluster-imagestore-apptypes]

应用程序包是一个磁盘目录，其中包含应用程序类型的 ApplicationManifest.xml 文件，该文件引用构成应用程序类型的每种服务类型的服务包。 例如，电子邮件应用程序类型的应用程序包可能包含对队列服务包、前端服务包和数据库服务包的引用。  

应用程序包目录中的文件会复制到 Service Fabric 群集的映像存储中。 然后，可基于此应用程序类型，创建在群集内运行的命名应用程序。 创建命名应用程序后，可以从应用程序类型的服务类型之一创建命名服务。 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>运行时：群集和节点、命名的应用程序、命名的服务、分区和副本
[Service Fabric 群集](service-fabric-deploy-anywhere.md)是一组通过网络连接在一起的虚拟机或物理计算机，你的微服务会在其中部署和管理。 群集可以扩展到成千上万台计算机。

构成群集的计算机或 VM 称为节点。 需为每个节点分配节点名称（字符串）。 节点具有各种特征，如放置属性。 每个计算机或 VM 都有一个自动启动 Windows 服务 `FabricHost.exe`，此服务在引导时开始运行，然后启动两个可执行文件：`Fabric.exe` 和 `FabricGateway.exe`。 这两个可执行文件构成了节点。 在开发或测试方案中，可以通过运行 `Fabric.exe` 和 `FabricGateway.exe` 的多个实例，在单台计算机或 VM 上托管多个节点。

命名应用程序是由执行一个或多个特定功能的命名服务组成的集合。 服务执行完整且独立的功能（它们可以独立于其他服务启动和运行），并由代码、配置和数据组成。 将应用程序包复制到映像存储后，可以通过指定应用程序包的应用程序类型（使用其名称/版本）在群集内创建应用程序的实例。 会为每个应用程序类型实例分配一个如下所示的 URI 名称：*fabric:/MyNamedApp*。 在群集中，可以从单个应用程序类型创建多个命名应用程序。 也可以从不同的应用程序类型创建命名应用程序。 可单独管理每个命名应用程序并设置其版本。

创建命名应用程序后，可以通过指定服务类型（使用其名称/版本），在群集中创建应用程序服务类型之一的实例（命名服务）。 需为每个服务类型实例分配一个 URI 名称，该名称归并到实例的命名应用程序的 URI 之下。 例如，如果在命名应用程序“MyNamedApp”中创建命名服务“MyDatabase”，则 URI 类似于： *fabric:/MyNamedApp/MyDatabase*。 在一个命名应用程序中可以创建一个或多个命名服务。 每个命名服务可以有自身的分区方案和实例/副本计数。 

有两种服务类型：无状态服务和有状态服务。 无状态服务不在服务中存储状态。 无状态服务根本没有持久性存储，或者将持久性状态存储在外部存储服务中，例如，存储在 Azure 存储、Azure SQL 数据库或 Azure Cosmos DB 中。 有状态服务将状态存储在服务内，并使用 Reliable Collections 或 Reliable Actors 编程模型来管理状态。 

创建命名服务时，需要指定一个分区方案。 包含大量状态的服务将跨分区拆分数据。 每个分区负责服务完整状态的一部分（分布在群集的节点上）。  

下图显示应用程序和服务实例、分区与副本之间的关系。

![服务中的分区和副本][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>分区、缩放和可用性
[分区](service-fabric-concepts-partitioning.md)并不是 Service Fabric 所独有的。 一种众所周知的分区形式是数据分区，也称为分片。 包含大量状态的有状态服务跨分区拆分数据。 每个分区负责服务完整状态的一部分。 

每个分区的副本分布在群集的节点上，以便命名服务状态进行[缩放](service-fabric-concepts-scalability.md)。 随着数据需求的增长，分区也会增长，Service Fabric 会在节点间重新平衡分区，以高效利用硬件资源。 如果向群集添加新节点，Service Fabric 会在新增加的节点间重新平衡分区副本。 应用程序总体性能提高，访问内存的争用减少。 如果没有高效使用群集中的节点，可以减少群集中节点的数量。 Service Fabric 会再次在减少的节点间重新平衡分区副本以更加充分使用每个节点上的硬件。

在分区中，无状态命名服务具有实例，而有状态命名服务具有副本。 通常，无状态命名服务只有一个分区，因为它们没有内部状态。 分区实例提供[可用性](service-fabric-availability-services.md)。 如果一个实例失败，其他实例可继续正常运行，然后 Service Fabric 会创建新的实例。 有状态命名服务在副本中保持其状态，每个分区都有自己的副本集。 在一个副本（以下称为“主副本”）上执行读取和写入操作。 因写入操作发生的状态更改会复制到其他多个副本（以下称为活动辅助副本）。 如果某个副本失败，Service Fabric 将从现有副本创建新副本。

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>无状态和有状态 Service Fabric 微服务
使用 Service Fabric，可以生成包含微服务或容器的应用程序。 无状态微服务（例如协议网关和 Web 代理）不在请求以及服务对请求的响应之外维持可变状态。 Azure 云服务辅助角色是无状态服务的一个示例。 有状态微服务（例如，用户帐户、数据库、设备、购物车、队列）维护除请求及其响应之外的可变、授权状态。 当今的 Internet 规模应用程序包含无状态和有状态微服务的组合。 

Service Fabric 的关键区别在于，大力注重使用[内置编程模型](service-fabric-choose-framework.md)或容器化有状态服务生成有状态服务。 [应用程序方案](service-fabric-application-scenarios.md)介绍了可使用有状态服务的方案。

为何要将有状态和无状态的微服务一同使用？ 有以下两个主要原因：

* 在同一台计算机上禁用代码和数据，可以生成高吞吐量、低延迟、容错联机事务处理 (OLTP) 服务。 一些示例包括互动商店、搜索、物联网 (IoT) 系统、交易系统、信用卡处理和欺诈检测系统，以及个人档案管理。
* 可以简化应用程序设计。 使用有状态微服务时，无需其他队列和缓存，而这在传统上是处理纯无状态应用程序的可用性和延迟要求所必需的。 有状态服务原本就具有高可用性和低延迟，减少了在应用程序中要作为一个整体进行管理的移动部件的数量。

## <a name="supported-programming-models"></a>支持的编程模型
Service Fabric 提供了多种方法来编写和管理服务。 服务可以使用 Service Fabric API 来充分利用平台的功能和应用程序框架。 服务还可以是采用任何语言编写的任意编译可执行程序，并在 Service Fabric 群集上托管。 有关详细信息，请参阅[支持的编程模型](service-fabric-choose-framework.md)。

### <a name="containers"></a>容器
默认情况下，Service Fabric 以进程形式部署和激活服务。 Service Fabric 还可以在[容器](service-fabric-containers-overview.md)中部署服务。 重要的是，可以在同一应用程序中混合使用进程中的服务和容器中的服务。 Service Fabric 支持在 Windows Server 2016 上部署 Linux 容器 和 Windows 容器。 可以在容器中部署现有应用程序、无状态服务或有状态服务。 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) 是一个用于编写服务的轻型框架，这些服务与 Service Fabric 平台集成并且受益于完整的平台功能集。 Reliable Services 可以是无状态的（与大多数服务平台类似，例如 Web 服务器或 Azure 云服务中的辅助角色），此时状态保存在外部解决方案中，例如 Azure DB 或 Azure 表存储。 Reliable Services 也可以是有状态的，此时状态使用 Reliable Collections 直接保存在服务中。 通过复制使状态具有[高可用性](service-fabric-availability-services.md)，以及通过[分区](service-fabric-concepts-partitioning.md)来分布状态，所有状态由 Service Fabric 自动管理。

### <a name="reliable-actors"></a>Reliable Actors
[Reliable Actor](service-fabric-reliable-actors-introduction.md) 框架在 Reliable Services 的基础上构建，是根据执行组件设计模式实现虚拟执行组件模式的应用程序框架。 Reliable Actor 框架使用称为执行组件的单线程执行的独立的计算单元和状态。 Reliable Actor 框架为执行组件提供内置通信，以及提供预设的状态暂留和扩展配置。

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric 与 [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 集成，作为用于生成 Web 和 API 应用程序的第一类编程模型。  在 Service Fabric 中可通过两种不同方法使用 ASP.NET Core：

- 作为来宾可执行文件托管。 这主要用于在 Service Fabric 上运行现有 ASP.NET Core 应用程序，无需更改代码。
- 在 Reliable Service 内部运行。 这可改善与 Service Fabric 运行时的集成，实现有状态的 ASP.NET Core 服务。

### <a name="guest-executables"></a>来宾可执行文件
[来宾可执行文件](service-fabric-guest-executables-introduction.md)是（采用任何语言编写的）任意现有可执行文件，并在 Service Fabric 群集及其他服务上托管。 来宾可执行文件不直接与 Service Fabric API 集成。 不过，它们仍受益于平台提供的功能，如自定义运行状况和负载报表以及服务可发现性（通过调用 REST API）。 它们还具有完整的应用程序生命周期支持。 

## <a name="application-lifecycle"></a>应用程序生命周期
与其他平台一样，Service Fabric 上的应用程序通常会经历以下几个阶段：设计、开发、测试、部署、升级、维护和删除。 Service Fabric 为云应用程序的整个应用程序生命周期提供一流的支持：从开发到部署、到日常管理和维护，再到最终解除授权。 服务模型使多个不同角色可以独立参与到应用程序生命周期中。 [Service Fabric 应用程序生命周期](service-fabric-application-lifecycle.md)一文提供了有关 API 的概述，以及在 Service Fabric 应用程序生命周期的各个阶段，它们是如何被不同角色所使用的。 

可以使用 [PowerShell cmdlet](/powershell/module/ServiceFabric/)、[CLI 命令](service-fabric-sfctl.md)、[C# API](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、[Java API](/java/api/overview/azure/servicefabric) 和 [REST API](/rest/api/servicefabric/) 管理整个应用生命周期。 还可以使用 [Azure Pipelines](service-fabric-set-up-continuous-integration.md) 或 [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md) 等工具来设置持续集成/持续部署管道。

## <a name="test-applications-and-services"></a>测试应用程序和服务
若要创建真正的云规模服务，请务必确保应用程序和服务能够经受住现实中发生的故障。 故障分析服务是在 Service Fabric 基础上专为测试服务构建的。 借助[故障分析服务](service-fabric-testability-overview.md)，可以引入有意义的故障，并对应用程序运行完整的测试方案。 这些故障和方案执行并验证服务在整个生命周期内要经历的大量状态和转换，所有一切都以受控、安全且一致的方式进行。

[操作](service-fabric-testability-actions.md)使用单独故障针对某个服务进行测试。 服务开发人员可将这些操作用作构造块来编写复杂的方案。 模拟故障的示例包括：

* 重新启动一个节点以模拟任意数量的重新启动计算机或 VM 的情形。
* 移动有状态服务的一个副本以模拟负载均衡、故障转移或应用程序升级。
* 在一个有状态服务上调用仲裁丢失以创建一种因为没有“足够”的“备份”或“辅助”副本来接收新的数据而无法继续写操作的情形。
* 在一个有状态服务上调用数据丢失以创建一种所有内存中的状态都被完全清除的情形。

[方案](service-fabric-testability-scenarios.md)是由一个或多个操作组成的复杂操作。 故障分析服务提供两种内置的完整方案：

* [混沌方案](service-fabric-controlled-chaos.md) - 在整个群集模拟连续交叉出现的故障（包括常规故障和非常规故障），时间跨度很长。
* [故障转移方案](service-fabric-testability-scenarios.md#failover-test) - 混沌测试方案针对特定服务分区的一个版本，同时不影响其他服务。

## <a name="clusters"></a>群集
[Service Fabric 群集](service-fabric-deploy-anywhere.md)是一组通过网络连接在一起的虚拟机或物理计算机，你的微服务将在其中部署和管理。 群集可以扩展到成千上万台计算机。 群集中的计算机或 VM 称为群集节点。 需为每个节点分配节点名称（字符串）。 节点具有各种特征，如放置属性。 每个计算机或 VM 都有一个自动启动服务 `FabricHost.exe`，此服务在引导时开始运行，并启动两个可执行文件：Fabric.exe 和 FabricGateway.exe。 这两个可执行文件构成了节点。 在测试方案中，可以通过运行 `Fabric.exe` 和 `FabricGateway.exe` 的多个实例，在单台计算机或 VM 上托管多个节点。

可在运行 Windows Server 或 Linux 的虚拟机或物理计算机上创建 Service Fabric 群集。 可在包含一组互连 Windows Server 或 Linux 计算机（本地计算机、Microsoft Azure 计算机或任何云提供商的计算机）的任何环境中部署和运行 Service Fabric 应用程序。

### <a name="clusters-on-azure"></a>Azure 上的群集
在 Azure 上运行 Service Fabric 群集可提供与其他 Azure 功能和服务的集成，这样可使群集的操作和管理更容易且更可靠。 群集是 Azure Resource Manager 资源，因此可以像 Azure 中的其他资源一样对群集进行建模。 Resource Manager 还可以轻松管理群集作为单个单元使用的所有资源。 Azure 上的群集已集成了 Azure 诊断和 Azure Monitor 日志。 群集节点类型是[虚拟机规模集](/azure/virtual-machine-scale-sets/index)，因此自动缩放功能是内置的。

可以通过 [Azure 门户](service-fabric-cluster-creation-via-portal.md)、[模板](service-fabric-cluster-creation-via-arm.md)或 [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md) 在 Azure 上创建群集。

如同在 Windows 上一样，可以使用 Linux 上的 Service Fabric 在 Linux 上构建、部署和管理高可用性、高度可缩放的应用程序。 Service Fabric 框架（Reliable Services 和 Reliable Actors）除了可在 C# (.NET Core) 中使用以外，也能在 Java on Linux 中使用。 还可以使用任何语言或框架来构建[来宾可执行的服务](service-fabric-guest-executables-introduction.md)。 还支持协调 Docker 容器。 Docker 容器可以运行使用 Service Fabric 框架的来宾可执行文件或本机 Service Fabric 服务。 有关详细信息，请参阅 [Linux 上的 Service Fabric](service-fabric-deploy-anywhere.md)。

某些在 Windows 上受支持的功能，在 Linux 上不受支持。 若要了解详细信息，请参阅 [Linux 上的 Service Fabric 与 Windows 上的 Service Fabric 之间的差异](service-fabric-linux-windows-differences.md)。

### <a name="standalone-clusters"></a>独立群集
Service Fabric 提供一个安装包，用于在本地或者任何云提供程序上创建独立的 Service Fabric 群集。 使用独立群集可随时随地托管群集。 如果数据受符合性或法规约束，或者需要将数据保留在本地，可以托管自己的群集和应用程序。 Service Fabric 应用程序可在多个托管环境中运行，而不发生任何变化。因此，在应用程序生成方面的知识在各个托管环境中都适用。 

[创建第一个独立的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)

尚不支持 Linux 独立群集。

### <a name="cluster-security"></a>群集安全性
必须保护群集以防止未经授权的用户连接到群集，特别是群集上正在运行生产工作负荷时。 虽然可以创建不安全的群集，但这样做会导致在向公共 Internet 公开管理终结点时，匿名用户可与它建立连接。 无法以后在不安全的群集上启用安全性：群集安全性在群集创建时启用。

群集安全方案包括：
* 节点到节点安全性
* 客户端到节点安全性
* 基于角色的访问控制 (RBAC)

有关详细信息，请阅读[保护群集](service-fabric-cluster-security.md)。

### <a name="scaling"></a>扩展
如果向群集添加新节点，Service Fabric 会在新增加的节点间重新平衡分区副本和实例。 应用程序总体性能提高，访问内存的争用减少。 如果没有高效使用群集中的节点，可以减少群集中节点的数量。 Service Fabric 会再次在减少的节点间重新平衡分区副本和实例以更加充分利用每个节点上的硬件。 可以在 Azure 上[手动](service-fabric-cluster-scale-in-out.md)或[以编程方式](service-fabric-cluster-programmatic-scaling.md)扩展群集。 可以[手动](service-fabric-cluster-windows-server-add-remove-nodes.md)扩展独立群集。

### <a name="cluster-upgrades"></a>群集升级
我们会定期发布新版本的 Service Fabric 运行时。 在群集上执行运行时或结构升级，以便始终运行[受支持的版本](service-fabric-support.md)。 除了结构升级，还可以更新群集配置（例如证书或应用程序端口）。

Service Fabric 群集是你拥有的，但部分由 Microsoft 管理的资源。 Microsoft 负责修补基础 OS 并在群集上执行结构升级。 当 Microsoft 发布新版本时，可以将群集设置为接收自动结构升级，或选择所需的受支持结构版本。 可通过 Azure 门户或 Resource Manager 设置结构和配置升级。 有关详细信息，请参阅[升级 Service Fabric 群集](service-fabric-cluster-upgrade.md)。 

独立群集是你完全拥有的资源。 负责修补基础 OS 和启动结构升级。 如果群集可以连接到 [https://www.microsoft.com/download](https://www.microsoft.com/download)，可以将群集设置为自动下载并预配新的 Service Fabric 运行时包。 然后会启动升级。 如果群集无法访问 [https://www.microsoft.com/download](https://www.microsoft.com/download)，可以从连接 Internet 的计算机手动下载新的运行时包，并启动升级。 有关详细信息，请参阅[升级独立 Service Fabric 群集](service-fabric-cluster-upgrade-windows-server.md)。

## <a name="health-monitoring"></a>运行状况监视
Service Fabric 引入了[运行状况模型](service-fabric-health-introduction.md)，用于在特定实体上标记不正常的群集和应用程序状态（例如群集节点和服务副本）。 运行状况模型使用运行状况报告器（系统组件和监视器）。 其目标是实现轻松快捷的诊断和修复。 服务写入程序需要预先考虑运行状况和如何[设计运行状况报告](service-fabric-report-health.md#design-health-reporting)。 应报告任何可能会影响运行状况的条件，尤其是如果它有助于标记出接近根源的问题。 在生产中大规模启动并运行服务后，运行状况信息可以减少调试和调查工作所需的时间和精力。

Service Fabric 报告器可监视感兴趣的已标识条件。 它们会根据其本地视图报告这些条件。 [运行状况存储](service-fabric-health-introduction.md#health-store)可汇总所有报告器发送的运行状况数据，从而确定实体的全局运行状况是否正常。 该模型应具有功能丰富、灵活且易于使用的特点。 运行状况报告的质量决定了群集运行状况视图的准确度。 错误显示不正常问题的误报会对升级或其他使用运行状况数据的服务产生负面影响。 修复服务和警报机制就是此类服务的例子。 因此，提供报表时需多加考量，才能让其以尽可能最佳的方式捕获感兴趣的条件。

可以基于以下内容完成报告：
* 受监视的 Service Fabric 服务副本或实例。
* 内部监视器部署为 Service Fabric 服务（例如，可监视状态和问题报告的 Service Fabric 无状态服务）。 可以在所有节点上部署监视器，也可以将监视器与受监视的服务关联。
* 在 Service Fabric 节点上运行，但未以 Service Fabric 服务形式实现的内部监视器。
* 从 Service Fabric 群集外探测资源的外部监视器（例如，诸如 Gomez 之类的监视服务）。

Service Fabric 组件报告包含群集中所有实体的运行状况。 [系统运行状况报告](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)提供有关群集和应用程序功能的可见性，并且通过运行状况标记问题。 对于应用程序和服务，系统运行状况报告从 Service Fabric 运行时的角度验证实体得到实现并且正常运行。 报告不对服务的业务逻辑进行任何运行状况监视，也不检测已停止响应的进程。 若要添加特定于服务逻辑的运行状况信息，请在服务中[执行自定义运行状况报告](service-fabric-report-health.md)。

Service Fabric 提供了多种方式查看在运行状况存储中聚合的[运行状况报告](service-fabric-view-entities-aggregated-health.md)：
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 或其他可视化工具。
* 运行状况查询（通过 [PowerShell](/powershell/module/ServiceFabric/)、[CLI](service-fabric-sfctl.md)、[C# FabricClient API](/dotnet/api/system.fabric.fabricclient.healthclient) 和 [Java FabricClient API](/java/api/system.fabric) 或 [REST API](/rest/api/servicefabric)）。
* 常规查询，返回将运行状况作为属性之一的实体的列表（通过 PowerShell、CLI、API 或 REST）。

## <a name="monitoring-and-diagnostics"></a>监视和诊断
[监视和诊断](service-fabric-diagnostics-overview.md)对任何环境中的开发、测试和部署应用程序和服务都至关重要。 如果规划和实施的监视与诊断有助于确保应用程序和服务在本地开发环境或在生产环境中按预期工作，则 Service Fabric 解决方案可以发挥最大的作用。

监视和诊断的主要目标是：

- 检测和诊断硬件和基础结构问题
- 检测软件和应用问题，减少服务停机时间
- 了解资源消耗情况，帮助推动运营决策
- 优化应用程序、服务和基础结构性能
- 生成业务见解并确定改进区域
 
监视和诊断的整体工作流程分为三个步骤：

1. 事件生成：这包括基础结构（群集）、平台和应用程序/服务级别的事件（日志、跟踪、自定义事件）
2. 事件聚合：需要先收集和聚合生成的事件才能显示这些事件
3. 分析：需要使事件可视化并能够以某种格式访问事件，以便按需进行分析和显示

有许多产品能够解决上述三个方面的问题，你可以针对每个方面任意选择不同的技术。 有关详细信息，请阅读 [Azure Service Fabric 的监视和诊断](service-fabric-diagnostics-overview.md)。

## <a name="next-steps"></a>后续步骤
* 了解如何[在 Azure 中创建群集](service-fabric-cluster-creation-via-portal.md)或[在 Windows 上创建独立群集](service-fabric-cluster-creation-for-windows-server.md)。
* 尝试使用 [Reliable Services](service-fabric-reliable-services-quick-start.md) 或 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 编程模型创建服务。
* 了解如何[从云服务迁移](service-fabric-cloud-services-migration-differences.md)。
* 学习[监视和诊断服务](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。 
* 了解如何[测试应用和服务](service-fabric-testability-overview.md)。
* 了解如何[管理和协调群集资源](service-fabric-cluster-resource-manager-introduction.md)。
* 浏览 [Service Fabric 示例](https://aka.ms/servicefabricsamples)。
* 了解 [Service Fabric 支持选项](service-fabric-support.md)。
* 阅读[团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)，了解文章和公告。


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
