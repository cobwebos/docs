---
title: Web 应用概述 | Microsoft Docs
description: 了解 Azure 应用服务如何帮助用户开发和托管 Web 应用程序
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 79828193e283f0dcb80035cae0c11b050a1639ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935624"
---
# <a name="web-apps-overview"></a>Web 应用概述

*Azure 应用服务 Web 应用*（简称 Web 应用）是用于托管 Web 应用程序、REST API 和移动后端的服务。 可以使用 .NET、.NET Core、Java、Ruby、Node.js、PHP 或 Python 等偏好的语言进行开发。 在基于 Windows 的环境中，应用程序可以轻松地运行和缩放。 对于基于 Linux 的环境，请参阅 [Linux 上的应用服务](containers/app-service-linux-intro.md)。 

Web 应用不仅可将 Microsoft Azure 的强大功能（例如安全性、负载均衡、自动缩放和自动管理）添加到应用程序， 我们还能利用其 DevOps 功能，例如来自 VSTS、GitHub、Docker 中心和其他源的持续部署，以及包管理、过渡环境、自定义域和 SSL 证书。 

使用应用服务时，需要支付 Azure 计算资源的使用费。 使用的计算资源量由运行 Web 应用的应用服务计划确定。 有关详细信息，请参阅 [Azure Web 应用中的应用服务计划](azure-web-sites-web-hosting-plans-in-depth-overview.md)。

## <a name="why-use-web-apps"></a>为何使用 Web 应用？

下面是应用服务 Web 应用的一些重要功能：

* **多个语言和框架** - Web 应用针对 ASP.NET、ASP.NET Core、Java、Ruby、Node.js、PHP 或 Python 提供一流支持。 我们还能以后台服务的形式运行 [PowerShell 和其他脚本或可执行文件](web-sites-create-web-jobs.md)。
* **DevOps 优化** - 使用 Visual Studio Team Services、GitHub、BitBucket、Docker 中心或 Azure 容器注册表设置[持续集成和部署](app-service-continuous-deployment.md)。 通过 [测试和过渡环境](web-sites-staged-publishing.md)提升更新。 在 Web 应用中使用 [Azure PowerShell](/powershell/azureps-cmdlets-docs) 或[跨平台命令行接口 (CLI)](/cli/azure/install-azure-cli) 管理应用。
* **具有高可用性的全局缩放** - 以手动或自动方式进行[增大](web-sites-scale.md)或[扩大](../monitoring-and-diagnostics/insights-how-to-scale.md)。 在 Microsoft 全球数据中心基础结构中的任意位置托管应用，并且应用服务 [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 承诺高可用性。
* **与 SaaS 平台和本地数据建立连接** - 从适用于企业系统（例如 SAP）的 50 多个[连接器](../connectors/apis-list.md)、SaaS 服务（例如 Salesforce）以及 Internet 服务（例如 Facebook）中进行选择。 使用[混合连接](../biztalk-services/integration-hybrid-connection-overview.md)和 [Azure 虚拟网络](web-sites-integrate-with-vnet.md)访问本地数据。
* **安全性和合规性** - 应用服务符合 [ISO、SOC 和 PCI](https://www.microsoft.com/en-us/trustcenter)的要求。 使用 [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) 或社交登录名（[Google](app-service-mobile-how-to-configure-google-authentication.md)、[Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)、[Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) 和 [Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)）对用户进行身份验证。 创建 [IP 地址限制](app-service-ip-restrictions.md)和[管理服务标识](app-service-managed-service-identity.md)。
* **应用程序模板** - 从 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 的大量应用程序模板列表中进行选择，例如 WordPress、Joomla 和 Drupal。
* **Visual Studio 集成** — Visual Studio 中的专用工具可简化创建、部署和调试工作。
* **API 和移动功能** - Web 应用针对 RESTful API 方案提供统包式 CORS 支持，通过启用身份验证、脱机数据同步、推送通知等功能简化移动应用方案。
* **无服务器代码** - 按需运行代码片段或脚本，无需显式预配或管理基础结构，并且只需为代码实际使用的计算时间付费（请参阅 [Azure Functions](/azure/azure-functions/)）。

除了应用服务中的 Web 应用，Azure 还提供可用来托管网站和 Web 应用程序的其他服务。 大多数情况下，Web 应用是最佳选择。  对于微服务体系结构，请考虑使用 [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)。 如果需要更好地控制运行代码的 VM，请考虑使用 [Azure 虚拟机](https://azure.microsoft.com/documentation/services/virtual-machines/)。 有关如何在这些 Azure 服务之间做出选择的详细信息，请参阅 [Azure 应用服务、虚拟机、Service Fabric 和云服务的比较](choose-web-site-cloud-service-vm.md)。

## <a name="next-steps"></a>后续步骤

创建第一个 Web 应用。

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby（Linux 上）](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
