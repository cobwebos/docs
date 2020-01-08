---
title: 使用 Azure Functions 和其他服务构建无服务器移动应用程序后端
description: 了解用于构建固态无服务器移动应用程序后端的计算服务。
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d59cca4b5c956134516aa2c8066894aa14e5d33d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453110"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>生成包含计算服务的移动后端组件
每个移动应用程序都需要一个后端负责数据存储、业务逻辑和安全性。 管理基础结构以托管和执行后端代码要求你调整、预配和缩放多个服务器。 还必须管理操作系统更新和所涉及的硬件并应用安全修补程序。 然后，需要监视所有这些基础结构组件的性能、可用性和容错能力。 

无服务器体系结构对于这种类型的方案非常有用，因为您没有要管理的服务器，也没有要管理的操作系统或相关软件或硬件更新。 无服务器体系结构可节省开发人员的时间和成本，这意味着在构建应用程序时更快投放市场和集中精力。

## <a name="benefits-of-compute"></a>计算的优点
- 服务器抽象意味着无需担心主机托管、修补和安全性，只需将精力集中在代码上。
- 即时且高效的缩放可确保按所需的任何规模自动设置资源或按需预配资源。
- 高可用性和容错能力。
- 微计费确保你只对代码实际运行的时间计费。
- 代码以你选择的语言在云中运行。

使用以下服务在移动应用中启用无服务器计算功能。

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/)是一种事件驱动的计算体验，你可以使用它来执行你的代码，以所选的编程语言编写，而不必担心服务器。 无需管理要在其上运行的应用程序或基础结构。 函数按需缩放，只需为代码运行的时间付费。 Azure 函数是实现移动应用程序 API 的好方法。 它们易于实现和维护，可通过 HTTP 访问。

**主要功能**
- 事件驱动的和可缩放的，你可以使用触发器和绑定来定义何时调用函数以及它所连接的数据。
- 引入你自己的依赖项，因为函数支持 NuGet 和 NPM，因此你可以使用常用的库。
- 集成安全性，使你可以通过 OAuth 提供程序（如 Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帐户）保护 HTTP 触发的函数。
- 简化了与不同[Azure 服务](/azure/azure-functions/functions-overview#integrations)和软件即服务（SaaS）产品的集成。
- 灵活的开发，使你可以直接在 Azure 门户中编写函数代码，或者通过 GitHub、Azure DevOps Services 和其他受支持的开发工具设置持续集成和部署代码。
- 函数运行时是开放源代码，在[GitHub](https://github.com/azure/azure-webjobs-sdk-script)上可用。
- 增强的开发体验：通过使用集成工具和内置 DevOps 功能进行监视，可以在本地进行编码、测试和调试。
- 用于开发的各种编程语言和宿主选项，例如C#Node.js、Java、JavaScript 或 Python。
- 按使用付费定价模型意味着只需为运行代码所用的时间付费。

**参考**
- [Azure 门户](https://portal.azure.com)
- [Azure Functions 文档](/azure/azure-functions/)
- [Azure Functions 开发人员指南](/azure/azure-functions/functions-reference)
- [快速入门](/azure/azure-functions/functions-create-first-function-vs-code)
- [示例](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
通过[Azure App Service](https://azure.microsoft.com/services/app-service/)，你可以采用所选编程语言构建和托管 web 应用和 RESTful api，而无需管理基础结构。 它提供自动缩放和高可用性，支持 Windows 和 Linux，并允许从 GitHub、Azure DevOps 或任何 Git 存储库进行自动部署。

**主要功能**
- 多语言和框架对 ASP.NET、ASP.NET Core、Java、Ruby、node.js、PHP 或 Python 的支持。 你还可以将 PowerShell 和其他脚本或可执行文件作为后台服务运行。
- 使用 Azure DevOps、GitHub、BitBucket、Docker 中心或 Azure 容器注册表进行持续集成和部署，实现 DevOps 优化。 使用 Azure PowerShell 或跨平台命令行接口（CLI）在应用服务中管理应用。
- 具有高可用性的全球规模，可手动或自动进行扩展或缩减。
- 与 SaaS 平台和本地数据的连接，可为企业系统（例如 SAP）、SaaS 服务（如 Salesforce）和 internet 服务（如 Facebook）选择50多个连接器。 使用混合连接和 Azure 虚拟网络访问本地数据。
- Azure App Service 符合 ISO、SOC 和 PCI 的要求。 通过 Azure Active Directory 或登录到社交媒体（如 Google、Facebook、Twitter 和 Microsoft）对用户进行身份验证。 创建 IP 地址限制并管理服务标识。
- 可从 Azure Marketplace 中的大量应用程序模板列表中进行选择的应用程序模板，如 WordPress、Joomla 和 Drupal。
- Visual Studio 与 Visual Studio 中的专用工具集成简化了创建、部署和调试工作。

**参考**
- [Azure 门户](https://portal.azure.com/)
- [Azure App Service 文档](/azure/app-service/)
- [快速入门](/azure/app-service/app-service-web-get-started-dotnet)
- [示例](/azure/app-service/samples-cli)
- [教程](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务
[Azure Kubernetes 服务 (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 管理托管的 Kubernetes 环境。 使用 AKS 可以快速轻松地部署和管理容器化应用程序，而无需具备容器业务流程方面的专业知识。 它还消除了正在进行的操作和维护的负担。 AKS 按需预配、升级和缩放资源，而无需使应用程序脱机。

**主要功能**
- 轻松将现有应用程序迁移到容器，并在 AKS 中运行。
- 简化基于微服务的应用程序的部署和管理。
- 保护 DevOps 的安全，实现速度和安全性之间的平衡，并在规模上更快速地交付代码。
- 使用 AKS 和 Azure 容器实例在容器实例内预配 pod （以秒为单位），可以轻松扩展。
- 按需部署和管理 IoT 设备。
- 使用 TensorFlow 和 KubeFlow 之类的工具训练机器学习模型。

**参考**
- [Azure 门户](https://portal.azure.com/)
- [Azure Kubernetes 服务文档](/azure/aks/)
- [快速入门](/azure/aks/kubernetes-walkthrough-portal)
- [教程](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure 容器实例
[Azure 容器实例](https://azure.microsoft.com/services/container-instances/)对于可在独立容器中运行的任何方案（如简单应用程序、任务自动化和生成作业）都是一个不错的解决方案。 快速开发应用，无需管理 Vm。

**主要功能**
- 快速启动时间，因为容器实例可在数秒内启动 Azure 中的容器，而无需预配和管理 Vm。
- 公共 IP 连接和自定义 DNS 名称。
- 保证你的应用程序在容器中的隔离程度与在 VM 中的隔离级别。
- 通过允许准确指定 CPU 核心和内存来实现最佳利用率的自定义大小。 费用取决于具体请求并按秒计收，因此可以根据实际需求来严格控制花费。
- 用于检索和保存状态的持久存储。 容器实例提供直接装载 Azure 文件共享的功能。
- 用相同 API 计划的 Linux 和 Windows 容器。

**参考**
- [Azure 门户](https://portal.azure.com/)
- [Azure 容器实例文档](/azure/container-instances/)
- [快速入门](/azure/container-instances/container-instances-quickstart-portal)
- [示例](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [教程](/azure/container-instances/container-instances-tutorial-prepare-app)