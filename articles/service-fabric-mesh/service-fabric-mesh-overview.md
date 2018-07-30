---
title: Azure Service Fabric 网格概述 | Microsoft Docs
description: 了解 Azure Service Fabric 网格。 使用 Service Fabric 网格可以部署和缩放应用程序，而无需考虑应用程序的基础结构需求。
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 06/27/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 65a9b1afcc0e1e6d4fcbb60a38ab0764e6fe2f18
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226438"
---
# <a name="what-is-service-fabric-mesh"></a>什么是 Service Fabric 网格？

Azure Service Fabric 网格是一个完全托管的服务，可让开发人员部署微服务应用程序，而无需管理虚拟机、存储或网络。 无需考虑赋能基础结构，就能省心地运行和缩放 Service Fabric 网格上托管的应用程序。  Service Fabric 网格包含由数千台计算机组成的群集。  开发人员察觉不到所有的群集操作。 只需上传代码，并指定所需的资源、可用性要求和资源限制即可。  Service Fabric 网格会自动分配应用程序部署请求的基础结构，同时还能处理基础结构故障，确保应用程序高度可用。 你只需关心应用程序的运行状况和响应能力，而不必考虑基础结构。  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

本文提供 Service Fabric 网格关键优势的概述。

## <a name="great-developer-experience"></a>极佳的开发人员体验

Service Fabric 网格支持可在容器中运行的任何编程语言或框架。 Visual Studio 2017 和 Visual Studio Code 工具支持针对 .NET 和 .NET Core 应用程序提供强大的编辑和调试体验。 

使用 Service Fabric 网格可以：

- 将现有的应用程序“直接迁移”到容器中，以大规模地现代化和运行当前应用程序。 
- 在 Azure 中大规模构建和部署新的微服务应用程序。  与其他 Azure 服务或者容器中运行的现有应用程序集成。 每个微服务是网络隔离的安全应用程序的一部分，其中定义了有关 CPU 核心、内存、磁盘空间等资源的调控策略。
- 无需对现有的应用程序进行更改，就能集成和扩展这些应用程序。 使用自己的虚拟网络将现有应用程序连接到新应用程序。  
- 通过迁移到 Service Fabric 网格，来现代化现有的云服务应用程序。  

## <a name="simple-operational-lifecycle"></a>简单的操作生命周期

轻松管理运行中的应用程序，包括应用程序升级和版本控制、监视应用程序，以及在生产环境中进行调试。 这些应用程序可以包括单个微服务，或者在其自身网络中隔离的多个微服务。 应用程序高效运行，部署、定位和故障转移时间很短。

使用 Service Fabric 网格可以：

- 无需显式预配和管理基础结构，即可部署和管理应用程序。  Service Fabric 网格会自动预配、升级、修补和维护底层基础结构。
- 使用集成式工具设置持续集成，以轻松打包和部署应用程序。
- 可以利用 Azure 资源管理器资源的所有功能（例如，审核线索和[基于角色的访问控制 (RBAC)](/azure/role-based-access-control/overview)），因为部署到 Azure 中 SF 网格服务的所有资源（例如应用程序、服务、机密等）都是 Azure 资源管理器资源。 
- 使用 [Azure 门户](https://portal.azure.com)、资源管理器模板或 Azure CLI/PowerShell 库部署和管理资源。
- 使用 [Application Insights](/azure/application-insights/)（或所选工具）设置操作监视和警报，以从平台捕获操作和诊断跟踪。 
- 使用 [Application Insights](/azure/application-insights/) 或所选工具访问应用程序模型发出的应用程序诊断信息。
- 通过为应用程序定义中的服务指定自动缩放规则来优化资源使用率。  （即将支持）
- 为应用程序创建网络隔离和安全边界，与 Hyper-V 容器结合使用时，此功能非常强大。 使用每个服务的多个 IP 以及每个应用程序的隔离虚拟网络，来隔离传入和传出服务的网络流量。  （即将支持） 


## <a name="mission-critical-platform-capabilities"></a>任务关键型平台功能

Service Fabric 网格可创建跨越 [Azure 可用性区域](/azure/availability-zones/az-overview)和/或地缘政治区域边界的群集集合。 使用一组意向（例如规模、硬件要求、持久性要求和安全策略）描述应用程序。  部署应用程序时，Service Fabric 网格会查找该应用程序的最佳运行位置。

使用 Service Fabric 网格可以：

- 利用高可用性、扩展/缩减功能、可发现性、业务流程、消息路由、可靠消息传递、无需停机升级、安全/机密管理、灾难恢复、状态管理、配置管理和分布式事务。
- 创建应用程序时在多个应用程序模型之间进行选择。
- 使用通过 Swagger 生成的语言特定绑定，利用通过 REST 终结点公开的平台功能。
- 跨[可用性区域](/azure/availability-zones/az-overview)和多个区域部署应用程序，以实现异地可靠性。
- 使用 Azure 提供的所有安全性和符合性功能。

## <a name="next-steps"></a>后续步骤

在 Visual Studio 中，只需执行几个步骤就能部署一个示例项目。 有关详细信息，请参阅[创建 ASP.NET Core 网站](service-fabric-mesh-quickstart-dotnet-core.md)。 


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
