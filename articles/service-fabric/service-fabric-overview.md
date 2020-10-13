---
title: Azure Service Fabric 概述
description: Service Fabric 是一个分布式系统平台，用于构建可缩放、可靠且易管理的微服务。
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contentperfq1
ms.openlocfilehash: 3c282178decc1a07b2c0acc102b279688c42d52e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91300643"
---
# <a name="overview-of-azure-service-fabric"></a>Azure Service Fabric 概述

Azure Service Fabric 是一个[分布式系统平台](#container-orchestration)，可方便用户打包、部署和管理可缩放且可靠的微服务和容器。 Service Fabric 还解决了[开发和管理](#application-lifecycle-management)云原生应用程序面临的重大难题。

Service Fabric 的一个重要差异化因素是，它重点用于构建有状态服务。 你可以使用 Service Fabric [编程模型](#stateless-and-stateful-microservices)或运行以任何语言或代码编写的容器化有状态服务。 除了 Azure 之外，你还可以[在任何位置创建 Service Fabric 群集](#any-os-any-cloud)，包括本地和其他公有云上的 Windows Server 和 Linux。

![Service Fabric 平台提供了生命周期管理、可用性、业务流程、编程模型、运行状况和监视、开发和操作工具，以及在 Azure、本地、其他云和你的开发计算机上进行自动缩放的功能][Image1]

Service Fabric 为当今很多 Microsoft 服务提供技术支持，包括 Azure SQL 数据库、Azure Cosmos DB、Cortana、Microsoft Power BI、Microsoft Intune、Azure 事件中心、Azure IoT 中心、Dynamics 365、Skype for Business 以及其他许多核心 Azure 服务。

## <a name="container-orchestration"></a>容器业务流程

Service Fabric 是 Microsoft 的[容器业务流程协调程序](service-fabric-cluster-resource-manager-introduction.md)，用于在计算机群集中部署和管理微服务，吸取了大规模运行 Microsoft 服务的经验教训。 Service Fabric 几秒内就可以高密度部署应用程序，即每台计算机部署成百上千个应用程序或容器。 使用 Service Fabric，可以在同一个应用程序中将进程中的服务和容器中的服务混用。

[详细了解 Service Fabric](service-fabric-content-roadmap.md) 核心概念、编程模型、应用程序生命周期、测试、群集和运行状况监视。

## <a name="stateless-and-stateful-microservices"></a>无状态和有状态微服务

Service Fabric 提供了一种复杂的轻型运行时，可支持无状态和有状态微服务。 Service Fabric 的一个重要差异化因素是它高度支持使用 Service Fabric [内置编程模型](service-fabric-choose-framework.md)或容器化有状态服务生成有状态服务。

详细了解受益于 Service Fabric 有状态服务的[应用程序方案](service-fabric-application-scenarios.md)。

## <a name="application-lifecycle-management"></a>应用程序生命周期管理

Service Fabric 为云应用程序（包括容器）的整个应用程序生命周期和 CI/CD 提供支持：从开发到部署，到日常监视、管理和维护，再到最终解除授权。 Service Fabric 与 [Azure Pipelines](https://www.visualstudio.com/team-services/)、[Jenkins](https://jenkins.io/index.html) 和 [Octopus Deploy](https://octopus.com/) 等 CI/CD 工具集成，并可与其他任何常用 CI/CD 工具配合使用。

有关应用程序生命周期管理的详细信息，请参阅[应用程序生命周期](service-fabric-application-lifecycle.md)。 若要将现有应用程序部署到 Service Fabric，请参阅[部署来宾可执行文件](service-fabric-deploy-existing-app.md)。

## <a name="any-os-any-cloud"></a>不限 OS 和云

可以在许多环境中（包括[在 Azure 中或本地](service-fabric-deploy-anywhere.md)、[在 Windows Server 或 Linux 中](service-fabric-linux-windows-differences.md)）为 Service Fabric 创建群集。 甚至可以在其他公有云上创建群集。 Service Fabric SDK 中的开发环境与生产环境完全相同，都不涉及模拟器。 也就是说，在本地开发群集上运行的内容是部署到其他环境的群集上的内容。

对于 [Windows 开发](service-fabric-get-started.md)，Service Fabric .NET SDK 已与 Visual Studio 和 PowerShell 集成。 对于 [Linux 开发](service-fabric-get-started-linux.md)，Service Fabric Java SDK 已与 Eclipse 集成，并且 Yeoman 被用来为 Java、.NET Core 和容器应用程序生成模板。

## <a name="compliance"></a>合规性

Azure Service Fabric 资源提供程序在所有 Azure 区域中都可用，并符合所有 Azure 合规性认证，这包括：SOC、ISO、PCI DSS、HIPAA 和 GDPR。 有关完整列表，请参阅 [Microsoft 合规性产品](https://www.microsoft.com/trustcenter/compliance/complianceofferings)。

## <a name="next-steps"></a>后续步骤

在 Azure Service Fabric 上创建并部署你的第一个应用程序：

> [!div class="nextstepaction"]
> [Service Fabric 快速入门][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
