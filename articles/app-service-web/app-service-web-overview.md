---
title: Web 应用概述 | Microsoft Docs
description: 了解 Azure App Service 如何帮助用户开发和托管 Web 应用程序
services: app-service\web
documentationcenter: ''
author: jaime-espinosa
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2016
ms.author: rachelap

---
# Web Apps 概述
*应用服务 Web 应用*是一个完全托管的计算平台，非常适合用来托管网站和 Web 应用程序。Microsoft Azure 提供的这个[平台即服务](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) 产品，使你可以在 Azure 维护用于运行和扩展应用的基础结构时，重点关注业务逻辑。

下面的 5 分钟视频介绍了 Azure App Service Web 应用。

[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]


## 应用服务中的 Web 应用是什么？
在应用服务中，*Web 应用*是 Azure 提供用来托管网站或 Web 应用程序的计算资源。

该计算资源可能位于共享虚拟机 (VM) 上，也可能位于专用虚拟机上，具体取决于你选择的定价层。你的应用程序代码在独立于其他客户的托管 VM 中运行。

你的代码可以使用 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 支持的任何语言或框架，例如 ASP.NET、Node.js、Java、PHP 或 Python。也可以在 Web 应用中运行使用 [PowerShell 和其他脚本语言](web-sites-create-web-jobs.md#acceptablefiles)的脚本。

有关可使用 Web 应用的典型应用程序方案的示例，请参阅 [Web 应用方案](https://azure.microsoft.com/documentation/scenarios/web-app/)以及 [Azure App Service、虚拟机、Service Fabric 和云服务的比较](choose-web-site-cloud-service-vm.md#scenarios)的**方案和建议**部分。

## 为何使用 Web Apps？
以下是适用于 Web 应用的一些主要应用服务功能：

* **多种语言和框架** — 应用服务为 ASP.NET、Node.js、Java、PHP 和 Python 提供一流支持。也可以在应用服务 VM 上运行 [PowerShell 和其他脚本或可执行文件](../app-service-web/web-sites-create-web-jobs.md)。
* **DevOps 优化** — 使用 Visual Studio Team Services、GitHub 或 BitBucket 设置[持续集成和部署](../app-service-web/app-service-continuous-deployment.md)。通过[测试和过渡环境](../app-service-web/web-sites-staged-publishing.md)提升更新。执行 [A/B 测试](../app-service-web/app-service-web-test-in-production-get-start.md)。使用 [Azure PowerShell](../powershell-install-configure.md) 或[跨平台命令行接口 (CLI)](../xplat-cli-install.md) 在应用服务中管理应用。
* **具有高可用性的全局缩放** — 以手动或自动方式[增加](../app-service-web/web-sites-scale.md)或[扩大](../azure-portal/insights-how-to-scale.md)。在 Microsoft 全球数据中心基础结构中的任意位置托管应用，并且应用服务 [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 承诺高可用性。
* **到 SaaS 平台和本地数据的连接** - 从适用于企业系统（例如 SAP、Siebel 和 Oracle）的 50 多个[连接器](../connectors/apis-list.md)、SaaS 服务（例如 Salesforce 和 Office 365）以及 Internet 服务（例如 Facebook 和 Twitter）中进行选择。使用[混合连接](../biztalk-services/integration-hybrid-connection-overview.md)和 [Azure 虚拟网络](../app-service-web/web-sites-integrate-with-vnet.md)访问本地数据。
* **安全性和合规性** — 应用服务符合 [ISO、SOC 和 PCI](https://www.microsoft.com/TrustCenter/) 的要求。
* **应用程序模板** - 从 [Azure 应用商店](https://azure.microsoft.com/marketplace/)的大量应用程序模板列表中进行选择，使用向导安装常用的开源软件，例如 WordPress、Joomla 和 Drupal。
* **Visual Studio 集成** — Visual Studio 中的专用工具可简化创建、部署和调试工作。

此外，Web 应用可以利用 [API 应用](../app-service-api/app-service-api-apps-why-best-platform.md)提供的功能（例如 CORS 支持）和[移动应用](../app-service-mobile/app-service-mobile-value-prop.md)提供的功能（例如推送通知）。有关应用服务中应用类型的详细信息，请参阅 [Azure App Service 概述](../app-service/app-service-value-prop-what-is.md)。

除了应用服务中的 Web 应用，Azure 还提供可用来托管网站和 Web 应用程序的其他服务。大多数情况下，Web Apps 是最佳选择。对于微服务体系结构，请考虑使用 [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)；如果需要更好地控制运行代码的 VM，请考虑使用 [Azure 虚拟机](https://azure.microsoft.com/documentation/services/virtual-machines/)。有关如何在这些 Azure 服务之间做出选择的详细信息，请参阅 [Azure App Service、虚拟机、Service Fabric 和云服务的比较](choose-web-site-cloud-service-vm.md)。

## 入门
若要首先在应用服务中向新 Web 应用部署示例代码，请遵循[在 5 分钟内将第一个 Web 应用部署到 Azure](app-service-web-get-started.md) 教程。你需要一个免费 Azure 帐户。

如果您想要在注册 Azure 帐户之前开始使用 Azure App Service，请转到[试用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，您可以在 App Service 中立即创建一个生存期较短的入门 Web 应用。你不需要使用信用卡，也不需要做出承诺。

<!---HONumber=AcomDC_0921_2016-->