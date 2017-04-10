---
title: "了解 Azure Service Fabric | Microsoft Docs"
description: "Service Fabric 概览和快速入门指南，其中应用程序由许多微服务组成，以提供扩展性和恢复能力。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/07/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 47b3fffb2d5c24b7473884e490be19ff17b61b61
ms.openlocfilehash: 9742523c0a1743ff5982e746aa3c99aed8934499
ms.lasthandoff: 02/21/2017


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>想要了解 Service Fabric 吗？
此学习路线图将帮助开始在 Service Fabric 上开发可缩放的、可靠的且易于管理的应用程序。

## <a name="the-five-minute-overview"></a>五分钟概述
Azure Service Fabric 是一个分布式系统平台，可让你更加轻松地打包、部署和管理可扩展且可靠的微服务，并在开发和管理云应用程序的过程中处理重大难题。 通过使用 Service Fabric，开发人员和管理员不仅可以避免解决复杂的基础结构问题，而且可以专注于实现可扩展、可靠且易于管理的所需的任务关键型工作负荷。 Service Fabric 代表用于生成和管理这些企业级的一级云规模应用程序的下一代中间件平台。  

这段简短 Channel9 视频介绍了 Service Fabric 和微服务：<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>详细概述
利用 Service Fabric，可以生成和管理可缩放且可靠的应用程序，该应用程序由在计算机的共享池（称为群集）上以非常高的密度运行的微服务组成。 它可以提供复杂运行时，用于构建分布式、可扩展的无状态和有状态微服务。 它还提供了全面的应用程序管理功能，用于设置、部署、监视、升级/修补和删除部署的应用程序。  请阅读 [Service Fabric 概述](service-fabric-overview.md)了解详细信息。

为什么选择微服务设计方法？ 所有应用程序会随着时间而发展。 成功的应用程序因为有实用性而发展。 现在对要求了解多少，未来又有何变化？ 在已知以后可以重新设计应用程序的情况下，有时向外寻求概念证明才是驱动因素。 另一方面，公司谈论构建云时都期望成长和使用量。 问题在于成长和规模不可预测。 我们希望能够快速获得原型，同时知道应用可缩放以对不可预测的成长和使用量做出反应。  [什么是微服务？](service-fabric-overview-microservices.md)说明了微服务设计方法如何应对这些挑战，以及如何创建可独立扩展或缩减、测试、部署和管理的微服务。

Service Fabric 提供了一个可靠而灵活的平台，使你能够编写和运行多种类型的业务应用程序与服务。  还可以运行任何现有应用程序（以任意语言编写）。  这些应用程序和微服务可以为无状态或有状态，并且它们在各虚拟机间的资源平衡，可最大限度提高工作效率。 Service Fabric 的独特体系结构使你可以在应用程序中执行近实时数据分析、内存中计算、并行事务和事件处理。 你可以根据不断变化的资源要求轻松向上或向下缩放应用程序（其实是扩展或缩减）。 阅读[应用程序方案](service-fabric-application-scenarios.md)，了解可创建的应用程序和服务的类别以及客户案例研究。

此内容较长的 Microsoft 虚拟大学视频介绍 Service Fabric 核心概念：<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="get-started-and-create-your-first-app"></a>开始使用并创建第一个应用 
使用 Service Fabric SDK 和工具，可在 Windows、Linux 或 MacOS 环境中开发应用，并将这些应用部署到在 Windows 或 Linux 上运行的群集。  以下指南指导如何在几分钟内部署应用。  运行第一个应用程序后，下载并运行[示例应用](http://aka.ms/servicefabricsamples)。 具体而言，从[入门示例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)开始

### <a name="on-windows"></a>在 Windows 上
Service Fabric SDK 包含一个用于 Visual Studio 的外接程序，它可提供用于创建、部署和调试 Service Fabric 应用程序的模板和工具。 这些主题会指导你完成在 Visual Studio 中创建你的第一个应用程序，并在开发计算机上运行该程序的过程。

[设置开发环境](service-fabric-get-started.md)
[创建第一个应用 (C#)](service-fabric-create-your-first-application-in-visual-studio.md)

#### <a name="practical-hands-on-labs"></a>实际动手实验
请尝试此全面的[动手实验第 1 部分](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx)，熟悉 Service Fabric 的端到端开发流程。  了解如何创建一个无状态服务、配置监视和运行状况报告并执行应用程序升级。 此后，请执行[动手实验第 2 部分](http://aka.ms/sflab2)，引导你创建有状态服务。


下面的 Channel9 视频会指导你完成在 Visual Studio 中创建 C# 应用的过程：  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>在 Linux 上
Service Fabric 提供用于在 Linux 上使用 .NET Core 和 Java 构建服务的 SDK。 这些主题会指导你完成在 Linux 上创建你的第一个 Java 或 C# 应用程序，并在开发计算机上运行该程序的过程。
[设置开发环境](service-fabric-get-started-linux.md)
[创建第一个应用 (Java)](service-fabric-create-your-first-linux-application-with-java.md)
[创建第一个应用 (C#)](service-fabric-create-your-first-linux-application-with-csharp.md)

以下 Microsoft 虚拟大学视频逐步讲解了在 Linux 上创建 Java 应用的过程：  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>在 MacOS 上
可在 MacOS X 上构建在 Linux 群集上运行的 Service Fabric 应用程序。 这些文章介绍了如何设置 Mac 进行开发，以及如何在 MacOS 上创建第一个 Java 应用程序并在 Ubuntu 虚拟机上运行它。
[设置开发环境](service-fabric-get-started-mac.md)
[创建第一个应用 (Java)](service-fabric-create-your-first-linux-application-with-java.md)

## <a name="core-concepts"></a>核心概念
此处介绍基础知识，有关详细概念和介绍，可参阅 [Service Fabric 术语](service-fabric-technical-overview.md)、[应用程序模型](service-fabric-application-model.md)和[支持的编程模型](service-fabric-choose-framework.md)。

<table><tr><th>核心概念</th><th>设计时</th><th>运行时</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-app-type-service-type-app-package-and-manifest-service-package-and-manifest"></a>设计时：应用类型、服务类型，应用包和清单、服务包和清单
应用程序类型是分配给服务类型集合的名称/版本。 在 ApplicationManifest.xml 文件中定义，在应用程序包目录中嵌入，并复制到 Service Fabric 群集的映像存储中。 然后，可基于此应用程序类型，创建在群集内运行的命名应用程序。 

服务类型是分配给服务的代码包、数据包、配置包的名称/版本。 在 ServiceManifest.xml 文件中定义，在服务包目录中嵌入，然后，应用程序包的 ApplicationManifest.xml 文件将引用该服务包目录。 在群集中创建命名应用程序后，你可以从应用程序类型的服务类型之一创建命名服务。 服务类型由其 ServiceManifest.xml 文件描述，并由在运行时加载的可执行代码服务配置设置和服务使用的静态数据组成。

![Service Fabric 应用程序类型和服务类型][cluster-imagestore-apptypes]

应用程序包是一个磁盘目录，其中包含应用程序类型的 ApplicationManifest.xml 文件，该文件引用构成应用程序类型的每种服务类型的服务包。 例如，电子邮件应用程序类型的应用程序包可能包含对队列服务包、前端服务包和数据库服务包的引用。 应用程序包目录中的文件将复制到 Service Fabric 群集的映像存储。 

服务包是一个磁盘目录，其中包含服务类型的 ServiceManifest.xml 文件，该文件引用服务类型的代码、静态数据和配置包。 应用程序类型的 ApplicationManifest.xml 文件引用服务包目录中的文件。 例如，服务包可能引用构成数据库服务的代码、静态数据和配置包。

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>运行时：群集和节点、命名应用，命名服务、分区和副本
[Service Fabric 群集](service-fabric-deploy-anywhere.md)是一组通过网络连接在一起的虚拟机或物理计算机，你的微服务将在其中部署和管理。 群集可以扩展到成千上万台计算机。

属于群集一部分的计算机或 VM 称为节点。 需为每个节点分配节点名称（字符串）。 节点具有各种特征，如放置属性。 每个计算机或 VM 都有一个自动启动 Windows 服务 FabricHost.exe，此服务在引导时开始运行，然后启动两个可执行文件：Fabric.exe 和 FabricGateway.exe。 这两个可执行文件构成了节点。 在开发和测试方案中，可以通过运行 Fabric.exe 和 FabricGateway.exe 的多个实例，在单台计算机或 VM 上托管多个节点。

命名应用程序是执行一个或多个特定功能的命名服务的集合。 服务执行完整且独立的功能（它们可以独立于其他服务启动和运行），并由代码、配置和数据组成。 将应用程序包复制到映像存储后，可以通过指定应用程序包的应用程序类型（使用其名称/版本）在群集内创建应用程序的实例。 将为每个应用程序类型实例分配一个类似于下面的 URI 名称：*fabric:/MyNamedApp*。 在群集中，你可以从单个应用程序类型创建多个命名应用程序。 还可以从不同的应用程序类型创建命名应用程序。 可单独管理每个命名应用程序并设置其版本。

创建命名应用程序后，可以通过指定服务类型（使用其名称/版本），在群集中创建应用程序服务类型（命名服务）之一的实例。 需为每个服务类型实例分配一个 URI 名称，该名称归并到实例的命名应用程序的 URI 之下。 例如，如果在命名应用程序“MyNamedApp”中创建命名服务“MyDatabase”，则 URI 将类似于：*fabric:/MyNamedApp/MyDatabase*。 在一个命名应用程序中可以创建一个或多个命名服务。 每个命名服务可以有自身的分区方案和实例/副本计数。 

有两种服务类型：无状态服务和有状态服务。  无状态服务的持久状态存储在 Azure 存储空间、Azure SQL 数据库、Azure DocumentDB 等外部存储服务中。 当服务根本没有永久性存储时，请使用无状态服务。 有状态服务使用 Service Fabric 通过其 Reliable Collections 或 Reliable Actors 编程模型管理服务状态。  

创建命名服务时，需要指定一个分区方案。 包含大量状态的服务将跨分区拆分数据，从而将数据分散在群集的节点上。 这样，命名服务的状态便可缩放。 在分区中，无状态命名服务具有实例，而有状态命名服务具有副本。 通常，无状态命名服务只有一个分区，因为它们没有内部状态。 分区实例提供可用性；如果一个实例失败，其他实例可继续正常运行，然后 Service Fabric 将创建新的实例。 有状态命名服务在副本中保持其状态，每个分区都有自身的副本集，其中包含保持同步的所有状态。 如果某个副本失败，Service Fabric 将从现有副本创建新副本。

下图显示应用程序和服务实例、分区与副本之间的关系。

![服务中的分区和副本][cluster-application-instances]

## <a name="supported-programming-models"></a>支持的编程模型
Service Fabric 提供了多种方法来编写和管理服务。 服务可以选择使用 Service Fabric API 来充分利用平台的功能和应用程序框架，或者服务可以只是采用任何语言编写的任何已编译的可执行程序，并且只是托管在 Service Fabric 群集上。 有关详细信息，请参阅[支持的编程模型](service-fabric-choose-framework.md)。

### <a name="guest-executables"></a>来宾可执行文件
[来宾可执行文件](service-fabric-deploy-existing-app.md)是采用任何语言编写的任意可执行文件，因此可以将现有应用程序托管在 Service Fabric 群集及其他服务上。 来宾可执行文件不直接与 Service Fabric API 集成，因而它们不会从平台所提供的完整功能集中获益，例如自定义健康和负载报告、服务终结点注册和有状态计算。

### <a name="containers"></a>容器
默认情况下，Service Fabric 以进程形式部署和激活这些服务。 Service Fabric 还可以部署[容器映像](service-fabric-containers-overview.md)中的服务。 重要的是，你可以在同一应用程序中混合进程中的服务和容器中的服务。  目前，Service Fabric 支持在 Linux 上部署 Docker 容器，还支持在 Windows Server 2016 上部署 Windows Server 容器。 在 Service Fabric 应用程序模型中，容器表示放置多个服务副本的应用程序主机。  可以使用 Service Fabric 在容器中部署现有应用程序、无状态服务或有状态服务。  

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) 是一个用于编写服务的轻型框架，这些服务与 Service Fabric 平台集成并且受益于完整的平台功能集。 Reliable Services 可以是无状态的（与大多数服务平台类似，例如 Web 服务器或 Azure 云服务中的辅助角色），此时状态保存在外部解决方案中，例如 Azure DB 或 Azure 表存储。 Reliable Services 也可以是有状态的，此时状态使用 Reliable Collections 直接保存在服务中。 通过复制使状态具有高可用性，以及通过分区来分布状态，所有状态由 Service Fabric 自动管理。

### <a name="reliable-actors"></a>Reliable Actors
[Reliable Actor](service-fabric-reliable-actors-introduction.md) 框架在 Reliable Services 的基础上构建，是根据执行组件设计模式实现虚拟执行组件模式的应用程序框架。 Reliable Actor 框架使用称为执行组件的单线程执行的独立的计算单元和状态。 Reliable Actor 为执行组件提供内置通信，以及提供预设的状态暂留和扩展配置。

## <a name="next-steps"></a>后续步骤
* 了解如何[在 Azure 中创建群集](service-fabric-cluster-creation-via-portal.md)或[在 Windows 上创建独立群集](service-fabric-cluster-creation-for-windows-server.md)。
* 尝试使用 [Reliable Services](service-fabric-reliable-services-quick-start.md) 或 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 编程模型创建服务。
* 了解[应用程序生命周期](service-fabric-application-lifecycle.md)。
* 学习[检查应用程序和群集运行状况](service-fabric-health-introduction.md)。
* 学习[监视和诊断服务](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。 
* 了解 [Service Fabric 支持选项](service-fabric-support.md)。


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

