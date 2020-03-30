---
title: 使用 Azure 函数和其他服务构建无服务器移动应用程序后端
description: 了解用于构建可靠、无服务器的后端移动应用程序的计算服务。
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240151"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>使用计算服务构建移动后端组件
每个移动应用程序都需要一个后端，负责数据存储、业务逻辑和安全。 管理基础结构以托管和执行后端代码需要您调整大小、预配和扩展多个服务器。 您还必须管理操作系统更新和所涉及的硬件，并应用安全修补程序。 然后，您需要监视所有这些基础结构组件的性能、可用性和容错性。 

无服务器体系结构对于此类方案非常有用，因为您没有服务器可供管理，也没有操作系统或相关的软件或硬件更新可供管理。 无服务器体系结构可节省开发人员的时间和成本，这意味着更快的上市时间和将精力集中在建筑应用程序上。

## <a name="benefits-of-compute"></a>计算的好处
- 服务器抽象意味着无需担心托管、修补和安全，这允许您只关注代码。
- 即时高效的扩展可确保以所需的任何规模自动或按需调配资源。
- 高可用性和容错能力。
- 微计费可确保仅在代码实际运行时为您付费。
- 代码在以您选择的语言编写的云中运行。

使用以下服务在移动应用中启用无服务器计算功能。

## <a name="azure-functions"></a>Azure Functions
[Azure 函数](https://azure.microsoft.com/services/functions/)是一种事件驱动的计算体验，可用于执行代码，这些代码使用您选择的编程语言编写，而无需担心服务器。 您不必管理应用程序或基础结构来运行它。 函数可按需扩展，并且仅为代码运行时付费。 Azure 函数是实现移动应用程序 API 的好方法。 它们易于实现和维护，可通过 HTTP 访问。

**主要功能**
- 事件驱动和可扩展，您可以使用触发器和绑定来定义何时调用函数及其连接的数据。
- 自带依赖项，因为函数支持 NuGet 和 NPM，因此您可以使用您喜爱的库。
- 集成安全性，以便您可以使用 OAuth 提供商（如 Azure 活动目录、Facebook、Google、Twitter 和 Microsoft 帐户）保护 HTTP 触发的功能。
- 简化与不同[Azure 服务和](/azure/azure-functions/functions-overview)软件即服务 （SaaS） 产品集成。
- 灵活的开发，以便您可以直接在 Azure 门户中编写函数代码，或者设置持续集成，并通过 GitHub、Azure DevOps 服务和其他受支持的开发工具部署代码。
- 函数运行时是开源的，在[GitHub](https://github.com/azure/azure-webjobs-sdk-script)上可用。
- 通过集成工具和内置 DevOps 功能进行监视，增强开发体验，您可以在本地使用首选编辑器或易于使用的 Web 界面进行编码、测试和调试。
- 各种编程语言和用于开发宿主选项，如 C#、Node.js、Java、JavaScript 或 Python。
- 按使用量付费定价模型意味着您只为运行代码所花费的时间付费。

**引用**
- [Azure 门户](https://portal.azure.com)
- [Azure 函数文档](/azure/azure-functions/)
- [Azure Functions 开发人员指南](/azure/azure-functions/functions-reference)
- [快速入门](/azure/azure-functions/functions-create-first-function-vs-code)
- [示例](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure 应用服务
使用[Azure 应用服务](https://azure.microsoft.com/services/app-service/)，您可以以您选择的编程语言构建和托管 Web 应用和 RESTful API，而无需管理基础结构。 它提供自动缩放和高可用性，同时支持 Windows 和 Linux，并支持从 GitHub、Azure DevOps 或任何 Git 存储库进行自动部署。

**主要功能**
- 对ASP.NET、ASP.NET核心、Java、Ruby、Node.js、PHP 或 Python 的多种语言和框架支持。 我们还能以后台服务的形式运行 PowerShell 和其他脚本或可执行文件。
- 通过与 Azure DevOps、GitHub、BitBucket、Docker 中心或 Azure 容器注册表的持续集成和部署进行 DevOps 优化。 在应用服务中，使用 Azure PowerShell 或跨平台命令行接口 (CLI) 来管理应用。
- 具有高可用性的全局规模，可手动或自动扩展或扩展。
- 连接到 SaaS 平台和本地数据，从 50 多个连接器中选择用于企业系统（如 SAP、SaaS 服务（如 Salesforce）和互联网服务（如 Facebook）。 使用混合连接和 Azure 虚拟网络访问本地数据。
- Azure 应用服务符合 ISO、SOC 和 PCI 标准。 使用 Azure 活动目录或登录 Google、Facebook、Twitter 和微软等社交媒体的用户进行身份验证。 创建 IP 地址限制和管理服务标识。
- 应用程序模板可从 Azure 应用商店中的应用程序模板（如 WordPress、Joomla 和 Drupal）中大量选择。
- Visual Studio 与 Visual Studio 中的专用工具集成可简化创建、部署和调试的工作。

**引用**
- [Azure 门户](https://portal.azure.com/)
- [Azure 应用服务文档](/azure/app-service/)
- [快速入门](/azure/app-service/app-service-web-get-started-dotnet)
- [示例](/azure/app-service/samples-cli)
- [教程](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务
[Azure Kubernetes 服务 (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 管理托管的 Kubernetes 环境。 使用 AKS 可以快速轻松地部署和管理容器化应用程序，而无需具备容器业务流程方面的专业知识。 它还消除了持续运营和维护的负担。 AKS 可按需提供、升级和扩展资源，而不会使应用程序脱机。

**主要功能**
- 轻松将现有应用程序迁移到容器并在 AKS 中运行。
- 简化基于微服务的应用程序的部署和管理。
- 为 AKS 提供安全 DevOps，实现速度和安全性之间的平衡，并更快地大规模交付代码。
- 通过使用 AKS 和 Azure 容器实例在容器实例中预配以秒为单位的窗格，轻松扩展。
- 按需部署和管理 IoT 设备。
- 使用 TensorFlow 和 KubeFlow 等工具对机器学习模型进行培训。

**引用**
- [Azure 门户](https://portal.azure.com/)
- [Azure 库伯内斯服务文档](/azure/aks/)
- [快速入门](/azure/aks/kubernetes-walkthrough-portal)
- [教程](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure 容器实例
[Azure 容器实例](https://azure.microsoft.com/services/container-instances/)是可在隔离容器中操作的任何方案（如简单应用程序、任务自动化和生成作业）的绝佳解决方案。 无需管理 VM 即可快速开发应用。

**主要功能**
- 容器实例的快速启动时间可以在数秒内在 Azure 中启动容器，而无需预配和管理 VM。
- 公共 IP 连接和自定义 DNS 名称。
- 虚拟机管理程序级安全性，可确保应用程序与在 VM 中隔离一样。
- 通过允许 CPU 内核和内存的精确规格，自定义大小以获得最佳利用率。 费用取决于具体请求并按秒计收，因此可以根据实际需求来严格控制花费。
- 用于检索和保留状态的持久存储。 容器实例提供直接安装 Azure 文件共享。
- 使用同一 API 安排的 Linux 和 Windows 容器。

**引用**
- [Azure 门户](https://portal.azure.com/)
- [Azure 容器实例文档](/azure/container-instances/)
- [快速入门](/azure/container-instances/container-instances-quickstart-portal)
- [示例](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [教程](/azure/container-instances/container-instances-tutorial-prepare-app)