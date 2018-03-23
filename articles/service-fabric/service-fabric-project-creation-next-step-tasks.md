---
title: Service Fabric 项目创建后续步骤 | Microsoft 文档
description: 了解刚才在 Visual Studio 中创建的应用程序项目。  了解如何使用教程生成服务以及有关开发 Service Fabric 服务的详细信息。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: rwike77
ms.openlocfilehash: 1e5b8523422aae00655b003bf15103d5467b4177
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="your-service-fabric-application-and-next-steps"></a>Service Fabric 应用程序和后续步骤
已创建 Azure Service Fabric 应用程序。 本文介绍了一些试验教程、项目的组成、你可能感兴趣的其他信息以及有可能执行的后续步骤。

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>快速入门教程、演练和示例
已准备就绪？  

按照 .NET 应用程序教程进行操作。 了解如何使用 ASP.NET Core 前端和监控状态的后端[构建应用](service-fabric-tutorial-create-dotnet-app.md)，如何为群集[部署应用程序](service-fabric-tutorial-deploy-app-to-party-cluster.md)、[配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) 以及如何[设置监视和诊断](service-fabric-tutorial-monitoring-aspnet.md)。

或尝试以下某个演练并创建第一个...
- [基于 Windows 的 C# Reliable Services 服务](service-fabric-reliable-services-quick-start.md) 
- [基于 Windows 的 C# Reliable Actors 服务](service-fabric-reliable-actors-get-started.md) 
- [基于 Windows 的来宾可执行服务](quickstart-guest-app.md) 
- [Windows 容器应用程序](service-fabric-get-started-containers.md) 

还可以尝试[示例应用程序](http://aka.ms/servicefabricsamples)。

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>还有任何疑问或反馈？  需要报告问题？
请浏览[常见问题](service-fabric-common-questions.md)，了解有关 Service Fabric 的功能及用法方面的解答。

[支持选项](service-fabric-support.md)列出了 StackOverflow 和 MSDN 的相关论坛和选项，可通过论坛提问并通过选项报告问题、获得支持和提交产品反馈。

## <a name="the-application-project"></a>应用程序项目
每个新应用程序都包含一个应用程序项目。 根据选择的服务类型，可能有一个或两个附加的项目。

应用程序项目包括：

* 对构成应用程序的服务的引用集。
* 三个发布配置文件（1-本地节点、5-本地节点和云），可用于维护首选项以适应不同的环境，诸如与群集终结点相关的首选项，以及是否按默认执行升级部署。
* 三个应用程序参数文件（同上），可用于维护环境特定的应用程序配置，例如，要为服务创建的分区数目。 了解如何[为多个环境配置应用程序](service-fabric-manage-multiple-environment-app-configuration.md)。
* 可使用部署脚本从命令行部署应用程序，或者通过自动持续集成和部署管道来部署应用程序。 深入了解如何[使用 PowerShell 部署应用程序](service-fabric-deploy-remove-applications.md)。
* 用于描述应用程序的应用程序清单。 可以在 ApplicationPackageRoot 文件夹下查找清单。 深入了解[应用程序和服务清单](service-fabric-application-model.md)。



## <a name="learn-more-about-the-programming-models"></a>了解有关编程模型的详细信息
Service Fabric 提供了多种方法来编写和管理服务。  以下是[无状态与有状态 Reliable Services](service-fabric-reliable-services-introduction.md)、[Reliable Actors](service-fabric-reliable-actors-introduction.md)、[容器](service-fabric-containers-overview.md)、[来宾可执行文件](service-fabric-guest-executables-introduction.md)和[无状态与有状态 ASP.NET Core 服务](service-fabric-reliable-services-communication-aspnetcore.md)的概述和概念信息。

## <a name="learn-about-service-communication"></a>了解服务通信相关信息
Service Fabric 应用程序由不同的服务组成，其中每个服务执行专门任务。 这些服务之间可以相互通信，并且存在连接到服务并与之通信的群集外客户端应用程序。 了解如何在 Service Fabric 中[设置与服务进行的通信以及服务之间的通信](service-fabric-connect-and-communicate-with-services.md)。 

## <a name="learn-about-configuring-application-security"></a>了解配置应用程序安全性的相关信息
可以保护群集中以不同用户帐户运行的应用程序。 Service Fabric 还有助于在部署时使用用户帐户来保护应用程序所用的资源，例如文件、目录和证书。 这样，即使在共享托管环境中，也可确保运行的应用程序彼此更安全。  了解如何[配置应用程序的安全策略](service-fabric-application-runas-security.md)。

应用程序可能包含敏感信息，例如存储连接字符串、密码或其他不应以明文形式处理的值。 了解如何[管理应用程序中的机密](service-fabric-application-secret-management.md)。

## <a name="learn-about-the-application-lifecycle"></a>了解应用程序生命周期的相关信息
与其他平台一样，Service Fabric 应用程序通常将经历以下几个阶段：设计、开发、测试、部署、升级、维护和删除。 [本文](service-fabric-application-lifecycle.md)提供了有关 API 的概述，并且介绍了在 Service Fabric 应用程序生命周期的各个阶段，它们如何被不同角色所使用。

## <a name="next-steps"></a>后续步骤
- [在 Azure 中创建 Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)。
- 使用 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 可视化群集（包括已部署的应用程序和物理布局）。
- [对服务进行版本控制和升级](service-fabric-application-upgrade-tutorial.md)


