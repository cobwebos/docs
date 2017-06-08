---
title: "Service Fabric 和容器概述 | Microsoft 文档"
description: "概述 Service Fabric，以及如何使用容器部署微服务应用程序。 本文概述容器的用法以及 Service Fabric 提供的功能。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/16/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: b8334d0ca0d1460edad9b0ef399e9b3428b1ef8d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017


---
# <a name="service-fabric-and-containers"></a>Service Fabric 和容器
> [!NOTE]
> Linux 的此功能处于预览状态。 
>   

## <a name="introduction"></a>介绍
Azure Service Fabric 是跨计算机群集的服务的[协调器](service-fabric-cluster-resource-manager-introduction.md)，已在 Microsoft 的大规模服务中使用并优化了多年。 开发服务的方式多种多样：从使用 [Service Fabric 编程模型](service-fabric-choose-framework.md)，到部署[来宾可执行文件](service-fabric-deploy-existing-app.md)，不一而足。 默认情况下，Service Fabric 以进程形式部署和激活这些服务。 进程能够以最快的速度激活、以最高的密度使用群集中的资源。 Service Fabric 还可以部署容器映像中的服务。 重要的是，你可以在同一应用程序中混合进程中的服务和容器中的服务。 

## <a name="containers-and-service-fabric-roadmap"></a>容器和 Service Fabric 路线图
在后续的多个版本中，Service Fabric 将继续在 Windows 和 Linux 上添加对容器的广泛支持，包括改进网络、资源约束、安全性、诊断、卷驱动程序的和工具支持（尤其是在 Visual Studio 中），以便使用容器映像部署服务的体验达到一流。 这样，你便可以选择使用任一容器来打包现有代码（例如 IIS MVC 应用）或 Service Fabric 编程模型，并且因为 Service Fabric 将这些一视同仁，你可以在应用程序中混合使用它们，从而为你提供部署代码方式的灵活性。 你可以根据具体的方案充分利用这些优势。

## <a name="what-are-containers"></a>什么是容器？
容器是可单独部署的封装组件，在相同内核中以隔离的实例运行，并利用操作系统级别的虚拟化。 这意味着，每个应用程序及其运行时、依赖项和系统库都在容器中运行，在容器各自的独立操作系统构造范围内拥有完全专属访问权限。 这种程度的安全性与资源隔离性，再加上可移植性，是将容器与 Service Fabric 配合使用带来的主要优势，否则就要在进程中运行服务。

容器是在应用程序中将基础操作系统虚拟化的一种虚拟化技术。 容器提供固定的环境，使应用程序以不同的隔离程度运行。 容器直接在内核顶层运行，在文件系统与其他资源之间界定了范围。 相比于虚拟机，容器具有以下优势：

* **小型**：容器使用单个存储空间，每一层的差异较小，因此更有效率。
* **快速启动**：容器不需要启动操作系统，因此启动和使用比虚拟机更快，通常只需几秒。
* **可移植性**：容器化的应用程序映像可以移植到云中或本地运行、移植到虚拟机中运行，或者直接在物理机上运行。
* **资源监管**：可以限制容器可在其主机上消耗的物理资源的容器。

## <a name="container-types"></a>容器类型
Service Fabric 支持以下类型的容器。

### <a name="docker-containers-on-linux"></a>Linux 上的 Docker 容器
Docker 提供了高级 API 在 Linux 内核容器上创建和管理容器。 Docker 中心是一个用于存储和检索容器映像的中心存储库。

有关如何执行此操作的演练，请阅读[将 Docker 容器部署到 Service Fabric](service-fabric-deploy-container-linux.md)。

### <a name="windows-server-containers"></a>Windows Server 容器
Windows Server 2016 提供两种不同类型的容器，它们的隔离程度有所不同。 Windows Server 容器与 Docker 容器相似，因为它们都能提供命名空间和文件系统隔离，但与它们运行所在的主机共享内核。 在 Linux 上，这种隔离一贯是由控制组 (cgroup) 和命名空间提供的，Windows Server 容器的行为与此类似。

Windows Hyper-V 容器提供更多隔离性和安全性，因为每个容器不与其他容器或主机共享操作系统内核。 由于具有这么高的安全隔离性，Hyper-V 容器适合用于对付恶意的多租户方案。

有关如何执行此操作的演练，请阅读[将 Windows 容器部署到 Service Fabric](service-fabric-deploy-container.md)。

下图显示了可在操作系统中使用的各种不同类型的虚拟化和隔离级别。
![Service Fabric 平台][Image1]

## <a name="scenarios-for-using-containers"></a>使用容器的方案
下面是典型示例，其中容器是一个不错的选择：

* **IIS 提升与移动**：如果你有想继续使用的现有 [ASP.NET MVC](https://www.asp.net/mvc) 应用，将它们放在容器中而不是迁移到 ASP.NET 核心。 这些 ASP.NET MVC 应用程序都依赖 Internet 信息服务 (IIS)。 可以从预先创建的 IIS 映像将这些应用打包成容器映像，然后使用 Service Fabric 将其部署。 有关如何创建 IIS 映像的信息，请参阅 [Windows Server 上的容器映像](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images)。
* **将容器和 Service Fabric 微服务混合**：可将现有容器映像用作应用程序的一部分。 例如，对于应用程序的 Web 前端，可以使用 [NGINX 容器](https://hub.docker.com/_/nginx/)；对于更密集的后端计算，可以使用有状态服务。
* **降低“干扰性邻居”服务的影响**：你可以使用容器的资源调控能力来限制服务在主机上使用的资源。 如果某些服务可能会消耗许多资源，因而影响其他服务的性能（例如，长时间运行的类似于查询的操作），请考虑将这些服务放入具有资源监管功能的容器中。

## <a name="service-fabric-support-for-containers"></a>Service Fabric 对容器的支持
目前，Service Fabric 支持在 Linux 上部署 Docker 容器，还支持在 Windows Server 2016 上部署 Windows Server 容器。 将来的版本会添加对 Hyper-V 容器的支持。

在 Service Fabric [应用程序模型](service-fabric-application-model.md)中，容器表示放置多个服务副本的应用程序主机。 支持以下容器方案：

* **来宾容器**：与[来宾可执行文件方案](service-fabric-deploy-existing-app.md)相同，可在容器中部署现有的应用程序。 示例包括 Node.js、JavaScript 或任意代码（可执行文件）。
* **容器中的无状态服务**：这是一些使用 Reliable Services 和 Reliable Actors 编程模型的无状态服务。 目前只有 Linux 支持此方案。 将来的版本会添加对 Windows 容器中无状态服务的支持。
* **容器中的有状态服务**：这是 Linux 上的一些使用 Reliable Actors 编程模型的有状态服务。 Linux 目前不支持 Reliable Services。  将来的版本会添加对 Windows 容器中有状态服务的支持。

Service Fabric 提供多种容器功能，可帮助你构建由容器化的微服务组成的应用程序。 这些服务称为容器化服务。 功能包括：

* 容器映像部署和激活。
* 资源调控。
* 存储库身份验证。
* 容器端口到主机端口的映射。
* 容器到容器的发现和通信。
* 能够配置和设置环境变量。

## <a name="next-steps"></a>后续步骤
本文介绍了容器，提到 Service Fabric 是一个容器协调器，此外，且 Service Fabric 具有支持容器的功能。 接下来，我们将演示其中的每项功能并说明其用法。

[将 Windows 容器部署到 Windows Server 2016 上的 Service Fabric](service-fabric-deploy-container.md)

[将 Docker 容器部署到 Linux 上的 Service Fabric](service-fabric-deploy-container-linux.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png

