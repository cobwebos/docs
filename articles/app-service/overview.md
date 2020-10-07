---
title: 概述
description: 了解 Azure 应用服务如何帮助用户开发和托管 Web 应用程序
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 1b72224441741990a1fc94400dfe718ea9d1b0b3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88961476"
---
# <a name="app-service-overview"></a>应用服务概述

Azure 应用服务是一项基于 HTTP 的服务，用于托管 Web 应用程序、REST API 和移动后端  。 可以使用 .NET、NET Core、Java、Ruby、Node.js、PHP 或 Python 等偏好的语言进行开发。 在基于 Windows 和 [Linux](#app-service-on-linux) 的环境中，应用程序都可以轻松地运行和缩放。

应用服务不仅可将 Microsoft Azure 的强大功能（例如安全性、负载均衡、自动缩放和自动管理）添加到应用程序。 你还可以利用其 DevOps 功能，例如，从 Azure DevOps、GitHub、Docker Hub 和其他源进行持续部署，包管理，过渡环境，自定义域和 TLS/SSL 证书。 

使用应用服务时，需要支付 Azure 计算资源的使用费。 使用的计算资源量由运行应用的应用服务计划确定。 有关详细信息，请参阅 [Azure 应用服务计划概述](overview-hosting-plans.md)。

## <a name="why-use-app-service"></a>为何使用应用服务？

下面是应用服务的一些主要功能：

* **多个语言和框架** - 应用服务针对 ASP.NET、ASP.NET Core、Java、Ruby、Node.js、PHP 或 Python 提供一流支持。 我们还能以后台服务的形式运行 [PowerShell 和其他脚本或可执行文件](webjobs-create.md)。
* **托管生产环境** - 应用服务会自动[修补并维护 OS 和语言框架](overview-patch-os-runtime.md)。 将时间花在编写优秀应用上，让 Azure 来考虑平台问题。
* 容器化和 Docker - 将应用 Docker 化并在应用服务中托管自定义 Windows 或 Linux 容器。 将多容器应用与 Docker Compose 和 Kubernetes 一起运行。 直接将 Docker 技能迁移到应用服务。
* **DevOps 优化** - 使用 Azure DevOps、GitHub、BitBucket、Docker 中心或 Azure 容器注册表设置[持续集成和部署](deploy-continuous-deployment.md)。 通过 [测试和过渡环境](deploy-staging-slots.md)提升更新。 在应用服务中，利用 [Azure PowerShell](/powershell/azure/) 或[跨平台命令行接口 (CLI)](/cli/azure/install-azure-cli) 来管理应用。
* **具有高可用性的全局缩放** - 以手动或自动方式进行[增大](manage-scale-up.md)或[扩大](../azure-monitor/platform/autoscale-get-started.md)。 在 Microsoft 全球数据中心基础结构中的任意位置托管应用，并且应用服务 [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 承诺高可用性。
* **与 SaaS 平台和本地数据建立连接** - 从适用于企业系统（例如 SAP）的 50 多个[连接器](../connectors/apis-list.md)、SaaS 服务（例如 Salesforce）以及 Internet 服务（例如 Facebook）中进行选择。 使用[混合连接](app-service-hybrid-connections.md)和 [Azure 虚拟网络](web-sites-integrate-with-vnet.md)访问本地数据。
* **安全性和合规性** - 应用服务符合 [ISO、SOC 和 PCI](https://www.microsoft.com/en-us/trustcenter)的要求。 使用 [Azure Active Directory](configure-authentication-provider-aad.md)、[Google](configure-authentication-provider-google.md)、[Facebook](configure-authentication-provider-facebook.md)、[Twitter](configure-authentication-provider-twitter.md) 或 [Microsoft 帐户](configure-authentication-provider-microsoft.md)对用户进行身份验证。 创建 [IP 地址限制](app-service-ip-restrictions.md)和[管理服务标识](overview-managed-identity.md)。
* **应用程序模板** - 从 [Azure 市场](https://azure.microsoft.com/marketplace/)的大量应用程序模板列表中进行选择，例如 WordPress、Joomla 和 Drupal。
* **Visual Studio 与 Visual Studio Code 集成** - Visual Studio 和 Visual Studio Code 中的专用工具可简化创建、部署和调试工作。
* **API 和移动功能** - 应用服务针对 RESTful API 方案提供统包式 CORS 支持，通过启用身份验证、脱机数据同步、推送通知等功能简化移动应用方案。
* **无服务器代码** - 按需运行代码片段或脚本，无需显式预配或管理基础结构，并且只需为代码实际使用的计算时间付费（请参阅 [Azure Functions](../azure-functions/index.yml)）。

除了应用服务，Azure 还提供可用来托管网站和 Web 应用程序的其他服务。 大多数情况下，应用服务是最佳选择。  对于微服务体系结构，请考虑使用 [Azure Spring-Cloud Service](../spring-cloud/index.yml) 或 [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)。  如果需要更好地控制运行代码的 VM，请考虑使用 [Azure 虚拟机](https://azure.microsoft.com/documentation/services/virtual-machines/)。 有关如何在这些 Azure 服务之间做出选择的详细信息，请参阅 [Azure 应用服务、虚拟机、Service Fabric 和云服务的比较](/azure/architecture/guide/technology-choices/compute-decision-tree)。

## <a name="app-service-on-linux"></a>Linux 上的应用服务

应用服务也可以在 Linux 上为支持的应用堆栈本地托管 Web 应用。 它还可以运行自定义 Linux 容器（也称为用于容器的 Web 应用）。

### <a name="built-in-languages-and-frameworks"></a>内置语言和框架

Linux 上的应用服务支持许多特定于语言的内置映像。 只需部署代码。 支持的语言包括：Node.js、Java (JRE 8 & JRE 11)、PHP、Python、.NET Core 和 Ruby。 运行 [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) 查看最新语言和支持的版本。 如果内置映像中不支持应用程序所需的运行时，则可以使用自定义容器部署运行时。

### <a name="limitations"></a>限制

- [共享](https://azure.microsoft.com/pricing/details/app-service/plans/)定价层不支持 Linux 上的应用服务。 
- 不能在同一个应用服务计划中混合使用 Windows 和 Linux 应用。  
- 在同一资源组内，不能在同一区域中混合使用 Windows 和 Linux 应用。
- Azure 门户仅显示当前可用于 Linux 应用的功能。 功能启用后，它们在门户上被激活。
- 部署到内置映像时，代码和内容将被分配一个 Web 内容存储卷，由 Azure 存储提供支持。 与容器文件系统的延迟相比，此卷的磁盘延迟更高，并且变化更大。 自定义容器选项将文件放在容器文件系统而非内容卷中，因此可能更适用于需要频繁以只读方式访问内容文件的应用。

## <a name="next-steps"></a>后续步骤

创建第一个 Web 应用。

> [!div class="nextstepaction"]
> [ASP.NET Core（在 Windows 或 Linux 上）](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET（在 Windows 上）](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP（在 Windows 或 Linux 上）](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby（Linux 上）](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js（在 Windows 或 Linux 上）](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java（在 Windows 或 Linux 上）](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python（在 Linux 上）](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML（在 Windows 或 Linux 上）](quickstart-html.md)

> [!div class="nextstepaction"]
> [自定义容器（Windows 或 Linux）](tutorial-custom-container.md)