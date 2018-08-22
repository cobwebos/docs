---
title: Service Fabric 和容器概述 | Microsoft 文档
description: 概述 Service Fabric，以及如何使用容器部署微服务应用程序。 本文概述容器的用法以及 Service Fabric 提供的功能。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: msfussell
ms.openlocfilehash: 6715142be7f40955861afa634bf6e2472c9f7294
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005769"
---
# <a name="service-fabric-and-containers"></a>Service Fabric 和容器

## <a name="introduction"></a>介绍

Azure Service Fabric 是一款分布式系统平台，可方便用户轻松打包、部署和管理可缩放的可靠微服务和容器。

Service Fabric 是用于跨计算机群集部署微服务的 Microsoft [容器业务流程协调程序](service-fabric-cluster-resource-manager-introduction.md)。 Service Fabric 借鉴了 Microsoft 多年以来在大规模运行服务的过程中学到的经验。

微服务的开发方法有多种，包括使用 [Service Fabric 编程模型](service-fabric-choose-framework.md)、[ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 或部署[任意选定代码](service-fabric-guest-executables-introduction.md)。 或者，如果你只想[部署和管理容器](service-fabric-containers-overview.md)，则Service Fabric 也是一个不错的选择。

默认情况下，Service Fabric 以进程形式部署和激活这些服务。 进程能够以最快的速度激活、以最高的密度使用群集中的资源。 Service Fabric 还可以部署容器映像中的服务。 此外，可以在同一应用程序中混合进程中的服务和容器中的服务。

若要立即在 Service Fabric 上体验容器，请尝试学习快速入门、教程或示例：  

[快速入门：将 Linux 容器应用程序部署到 Service Fabric](service-fabric-quickstart-containers-linux.md)  
[快速入门：将 Windows 容器应用程序部署到 Service Fabric](service-fabric-quickstart-containers.md)  
[将现有 .NET 应用容器化](service-fabric-host-app-in-a-container.md)  
[Service Fabric 容器示例](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>什么是容器

容器提供一个不可变的环境用于运行应用程序，可以解决在不同计算环境中可靠运行应用程序的问题。 容器将应用程序及其所有依赖项（例如库和配置文件）包装到该应用程序自身的独立“框架”中，该框架包含在容器内部运行软件所需的一切组件。 无论容器在何处运行，其内部的应用程序始终会获得所需的一切组件来运行适当版本的依赖库、任何配置文件和需要运行的其他所有项目。

容器直接在内核顶层运行，在文件系统与其他资源之间界定了范围。 容器内部的应用程序不知道其容器外部的其他任何应用程序或进程。 每个应用程序及其运行时、依赖项和系统库都在容器中运行，在容器各自的独立操作系统范围内拥有完全专属访问权限。 除了方便提供应用程序所需的所有依赖项，使应用程序能够在不同的计算环境中运行以外，安全性和资源隔离也是在 Service Fabric 中使用容器的重要优势 - 否则需要在进程中运行服务。

相比于虚拟机，容器具有以下优势：

* 小：容器使用单个存储空间和层的版本与更新，提高了效率。
* **快**：容器无需启动整个操作系统，因此启动速度更快，通常在几秒内即可启动。
* **可移植性**：容器化的应用程序映像可以移植到云中或本地运行、移植到虚拟机中运行，或者直接在物理机上运行。
* **资源监管**：可以限制容器可在其主机上消耗的物理资源的容器。

### <a name="container-types-and-supported-environments"></a>容器类型和受支持的环境

Service Fabric 支持 Linux 和 Windows 上的容器，也支持 Windows 上的 Hyper-V 隔离模式。

#### <a name="docker-containers-on-linux"></a>Linux 上的 Docker 容器

Docker 提供 API 用于在 Linux 内核容器上创建和管理容器。 Docker 中心提供一个用于存储和检索容器映像的中心存储库。
有关基于 Linux 的教程，请参阅[在 Linux 上创建第一个 Service Fabric 容器应用程序](service-fabric-get-started-containers-linux.md)。

#### <a name="windows-server-containers"></a>Windows Server 容器

Windows Server 2016 提供两种不同类型的容器，它们的隔离程度有所不同。 Windows Server 容器与 Docker 容器相似，因为两者都能提供命名空间和文件系统隔离，但与它们运行所在的主机共享内核。 在 Linux 上，这种隔离一贯是由控制组 (cgroup) 和命名空间提供的，Windows Server 容器的行为与此类似。

包含 Hyper 支持的 Windows 容器提供更多隔离性和安全性，因为任何容器都不与其他容器或主机共享操作系统内核。 由于具有这么高的安全隔离性，启用 Hyper-V 的容器适合用于对付潜在恶意的多租户方案。
有关基于 Windows 的教程，请参阅[在 Windows 上创建第一个 Service Fabric 容器应用程序](service-fabric-get-started-containers.md)。

下图显示了可用的各种不同类型的虚拟化和隔离级别。
![Service Fabric 平台][Image1]

## <a name="scenarios-for-using-containers"></a>使用容器的方案

下面是典型示例，其中容器是一个不错的选择：

* **IIS 直接迁移**：可将现有 [ASP.NET MVC](https://www.asp.net/mvc) 应用放在容器中，而无需将其迁移到 ASP.NET Core。 这些 ASP.NET MVC 应用都依赖于 Internet Information Services (IIS)。 可以从预先创建的 IIS 映像中将这些应用程序打包成容器映像，然后再使用 Service Fabric 部署。 有关 Windows 容器的信息，请参阅 [Windows Server 上的容器映像](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)。

* **将容器和 Service Fabric 微服务混合**：可将现有容器映像用作应用程序的一部分。 例如，对于应用程序的 Web 前端，可以使用 [NGINX 容器](https://hub.docker.com/_/nginx/)；对于更密集的后端计算，可以使用有状态服务。

* **降低“干扰性邻居”服务的影响**：可以使用容器的资源调控能力来限制服务在主机上使用的资源。 如果某些服务可能会消耗许多资源，因而影响其他服务的性能（例如，长时间运行的类似于查询的操作），请考虑将这些服务放入具有资源监管功能的容器中。

## <a name="service-fabric-support-for-containers"></a>Service Fabric 对容器的支持

Service Fabric 支持在 Linux 上部署 Docker 容器，在 Windows Server 2016 上部署 Windows Server 容器，同时支持 Hyper-V 隔离模式。 

Service Fabric 提供一个[应用程序模型](service-fabric-application-model.md)，其中的容器表示放置多个服务副本的应用程序主机。 Service Fabric 还支持[来宾可执行方案](service-fabric-guest-executables-introduction.md)，在其中不是使用内置的 Service Fabric 编程模型，而是在容器内打包以任何语言或框架编写的现有应用程序。 此方案是容器的常见用例。

还可以[在容器内部运行 Service Fabric 服务](service-fabric-services-inside-containers.md)。 目前针对在容器内运行 Service Fabric 服务的支持有限。

Service Fabric 提供多种容器功能，可帮助构建由容器化的微服务组成的应用程序，例如：

* 容器映像部署和激活。
* 资源治理包括默认设置 Azure 群集上的资源值。
* 存储库身份验证。
* 容器端口到主机端口的映射。
* 容器到容器的发现和通信。
* 能够配置和设置环境变量。
* 能够设置容器的安全凭据。
* 容器的不同网络模式选择。

有关 Azure 上的容器支持的综合概述，例如，如何使用 Azure Kubernetes 服务创建 Kubernetes 群集、如何在 Azure 容器注册表中创建专用的 Docker 注册表，等等，请参阅 [Azure 容器](https://docs.microsoft.com/azure/containers/)。

## <a name="next-steps"></a>后续步骤

本文已介绍 Service Fabric 为运行容器而提供的支持。 接下来，我们将演示其中的每项功能并说明其用法。

[在 Linux 上创建第一个 Service Fabric 容器应用程序](service-fabric-get-started-containers-linux.md)  
[在 Windows 上创建第一个 Service Fabric 容器应用程序](service-fabric-get-started-containers.md)  
[了解更多关于 Windows 容器的信息](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png