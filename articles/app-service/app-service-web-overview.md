---
title: "Web 应用概述 | Microsoft Docs"
description: "了解 Azure 应用服务如何帮助用户开发和托管 Web 应用程序"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: c1b3feb79328269f4692d744e7a473397d0328c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="web-apps-overview"></a>Web 应用概述
*应用服务 Web 应用* 是一个完全托管的计算平台，非常适合用来托管网站和 Web 应用程序。 Microsoft Azure 提供的这个 [平台即服务](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) 产品使你可重点关注业务逻辑，同时 Azure 负责维护用于运行和扩展应用的基础结构。

下面的 5 分钟视频介绍了 Azure 应用服务 Web 应用。

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

## <a name="what-is-a-web-app-in-app-service"></a>应用服务中的 Web 应用是什么？
在应用服务中， *Web 应用* 是 Azure 提供用来托管网站或 Web 应用程序的计算资源。  

该计算资源可能位于共享虚拟机 (VM) 上，也可能位于专用虚拟机上，具体取决于选择的定价层。 应用程序代码在独立于其他客户的托管 VM 中运行。

代码可使用 Azure App Service 支持的任何语言或框架，例如 ASP.NET、Node.js、Java、PHP 或 Python。 也可以在 Web 应用中运行使用 [PowerShell 和其他脚本语言](web-sites-create-web-jobs.md#acceptablefiles) 的脚本。

有关可使用 Web 应用的典型应用程序方案示例，请参阅 [Azure 应用服务、虚拟机、Service Fabric 和云服务组件](choose-web-site-cloud-service-vm.md#scenarios)中的 [Web 应用方案](https://azure.microsoft.com/documentation/scenarios/web-app/)以及**方案和建议**部分。

## <a name="why-use-web-apps"></a>为何使用 Web 应用？
以下是适用于 Web 应用的一些主要应用服务功能：

* **多种语言和框架** — 应用服务为 ASP.NET、Node.js、Java、PHP 和 Python 提供一流支持。 也可以在应用服务 VM 上运行 [PowerShell 和其他脚本或可执行文件](web-sites-create-web-jobs.md) 。
* **DevOps 优化** — 使用 Visual Studio Team Services、GitHub 或 BitBucket 设置 [持续集成和部署](app-service-continuous-deployment.md) 。 通过 [测试和过渡环境](web-sites-staged-publishing.md)提升更新。 在应用服务中，利用 [Azure PowerShell](/powershell/azureps-cmdlets-docs) 或[跨平台命令行接口 (CLI)](../cli-install-nodejs.md) 来管理应用。
* **具有高可用性的全局缩放** - 以手动或自动方式进行[增大](web-sites-scale.md)或[扩大](../monitoring-and-diagnostics/insights-how-to-scale.md)。 在 Microsoft 全球数据中心基础结构中的任意位置托管应用，并且应用服务 [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 承诺高可用性。
* **到 SaaS 平台和本地数据的连接** - 从适用于企业系统（例如 SAP、Siebel 和 Oracle）的 50 多个 [连接器](../connectors/apis-list.md) 、SaaS 服务（例如 Salesforce 和 Office 365）以及 Internet 服务（例如 Facebook 和 Twitter）中进行选择。 使用[混合连接](../biztalk-services/integration-hybrid-connection-overview.md)和 [Azure 虚拟网络](web-sites-integrate-with-vnet.md)访问本地数据。
* **安全性和合规性** - 应用服务符合 [ISO、SOC 和 PCI](https://www.microsoft.com/TrustCenter/)的要求。
* **应用程序模板** - 从 [Azure 应用商店](https://azure.microsoft.com/marketplace/) 的大量应用程序模板列表中进行选择，使用向导安装流行的开源软件，例如 WordPress、Joomla 和 Drupal。
* **Visual Studio 集成** — Visual Studio 中的专用工具可简化创建、部署和调试工作。

此外，Web 应用可利用 [API 应用](app-service-web-tutorial-rest-api.md)提供的 CORS 支持等功能和[移动应用](../app-service-mobile/app-service-mobile-value-prop.md)提供的推送通知等功能。 

除了应用服务中的 Web 应用，Azure 还提供可用来托管网站和 Web 应用程序的其他服务。 大多数情况下，Web 应用是最佳选择。  对于微服务体系结构，请考虑使用 [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)；如果需要更好地控制运行代码的 VM，请考虑使用 [Azure 虚拟机](https://azure.microsoft.com/documentation/services/virtual-machines/)。 有关如何在这些 Azure 服务之间做出选择的详细信息，请参阅 [Azure 应用服务、虚拟机、Service Fabric 和云服务的比较](choose-web-site-cloud-service-vm.md)。

## <a name="getting-started"></a>入门
若要开始在应用服务中向新 Web 应用部署示例代码，请遵循下拉框中的某篇教程。 需要一个免费 Azure 帐户。

> [!div class="op_single_selector"]
> * [在 5 分钟内将第一个 ASP.NET Web 应用部署到 Azure](app-service-web-get-started-dotnet.md)
> * [在 5 分钟内将第一个 PHP Web 应用部署到 Azure](app-service-web-get-started-php.md)
> * [在 5 分钟内将第一个 Node.js Web 应用部署到 Azure](app-service-web-get-started-nodejs.md)
> * [在 5 分钟内将第一个 Java Web 应用部署到 Azure](app-service-web-get-started-java.md)
> * [在 5 分钟内将第一个 Python Web 应用部署到 Azure](app-service-web-get-started-python.md)
> * [在 5 分钟内将第一个 HTML 站点部署到 Azure](app-service-web-get-started-html.md)
> 
> 

> [!NOTE]
> 无需 Azure 帐户即可 [试用应用服务](https://azure.microsoft.com/try/app-service/) 。 创建入门级应用并使用长达一小时 — 无需信用卡，也无需做出承诺。
> 
> 
