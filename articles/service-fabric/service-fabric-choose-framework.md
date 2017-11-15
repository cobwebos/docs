---
title: "Service Fabric 编程模型概述 | Microsoft 文档"
description: "Service Fabric 提供了两个框架用于构建服务：执行组件框架和服务框架。 它们在简单性和控制力方面具有截然不同的取舍。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 237b8396b56fdec86cc005c121646556825d8e98
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="service-fabric-programming-model-overview"></a>Service Fabric 编程模型概述
Service Fabric 提供了多种方法来编写和管理服务。 服务可以选择使用 Service Fabric API 来充分利用平台的功能和应用程序框架。 服务还可以是采用任何语言编写的任意已编译可执行程序，也可以是在 Service Fabric 群集上直接托管的容器中运行的代码。

## <a name="guest-executables"></a>来宾可执行文件
[来宾可执行文件](service-fabric-deploy-existing-app.md)是（采用任何语言编写的）任意现有可执行文件，可在应用程序中作为服务运行。 来宾可执行文件不直接调用 Service Fabric SDK API。 但是，它们仍受益于平台提供的功能，如服务可发现性、自定义运行状况和负载报告（通过调用 Service Fabric 公开的 REST API）。 它们还具有完整的应用程序生命周期支持。

从部署第一个[来宾可执行文件应用程序](service-fabric-deploy-existing-app.md)开始使用来宾可执行文件。

## <a name="containers"></a>容器
默认情况下，Service Fabric 以进程形式部署和激活这些服务。 Service Fabric 还可以在[容器](service-fabric-containers-overview.md)中部署服务。 Service Fabric 支持在 Windows Server 2016 上部署 Linux 容器 和 Windows 容器。 可以从任何容器存储库中提取容器映像，并将其部署到计算机上。 可以在容器中将现有应用程序部署为来宾可执行文件、Service Fabric 无状态/有状态可靠服务或 Reliable Actors，并可以将进程中的服务和容器中的服务混用于同一应用程序。

[了解有关在 Windows 或 Linux 中容器化服务的详细信息](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services
Reliable Services 是一个用于编写与 Service Fabric 平台集成的服务的轻型框架，并且受益于完整的平台功能集。 Reliable Services 提供最小 API 集合，该集合允许 Service Fabric 运行时管理服务的生命周期，以及允许服务与运行时进行交互。 应用程序框架为最简化，可便于完全控制设计和实现选项，并可用于托管其他任何应用程序框架，如 ASP.NET Core。

Reliable Services 可以无状态，类似于大多数服务平台，如 Web 服务器。也就是说，创建的每个服务实例都是平等的，并且状态保存在外部解决方案中，如 Azure DB 或 Azure 表存储。

Reliable Services 也可以是有状态的，专门用于 Service Fabric，其状态使用 Reliable Collections 直接保存在服务中。 通过复制使状态具有高可用性，以及通过分区来分布状态，所有状态由 Service Fabric 自动管理。

[详细了解 Reliable Services](service-fabric-reliable-services-introduction.md)，或开始[编写首个 Reliable Services 服务](service-fabric-reliable-services-quick-start.md)。

## <a name="aspnet-core"></a>ASP.NET Core
ASP.NET Core 是新的开源跨平台框架，用于构建现代基于云的连接 Internet 的应用程序，如 Web 应用、IoT 应用和移动后端。 Service Fabric 与 ASP.NET Core 集成，因此你可以编写无状态和有状态的 ASP.NET Core 应用程序，充分利用 Reliable Collections 和 Service Fabric 的高级编排功能。

[了解有关 Service Fabric 中 ASP.NET Core 的详细信息](service-fabric-reliable-services-communication-aspnetcore.md)，或通过[编写第一个 ASP.NET Core Service Fabric 应用程序](service-fabric-add-a-web-frontend.md)开始使用。

## <a name="reliable-actors"></a>Reliable Actors
Reliable Actor 框架在 Reliable Services 的基础上构建，是根据执行组件设计模式实现虚拟执行组件模式的应用程序框架。 Reliable Actor 框架使用称为执行组件的单线程执行的独立的计算单元和状态。 Reliable Actor 为执行组件提供内置通信，以及提供预设的状态暂留和扩展配置。

由于 Reliable Actors 自身是在 Reliable Services 基础上构建的应用程序框架，所以它完全与 Service Fabric 平台集成，并且获益于平台所提供的完整功能集。

[详细了解 Reliable Actors](service-fabric-reliable-actors-introduction.md)，或开始[编写首个 Reliable Actors 服务](service-fabric-reliable-actors-get-started.md)


[使用 ASP.NET Core 构建前端服务](service-fabric-add-a-web-frontend.md)

## <a name="next-steps"></a>后续步骤
[Service Fabric 和容器概述](service-fabric-containers-overview.md)

[Reliable Services 概述](service-fabric-reliable-services-introduction.md)

[Reliable Actors 概述](service-fabric-reliable-actors-introduction.md)

[Service Fabric 和 ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)




