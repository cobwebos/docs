<!-- not suitable for Mooncake -->

<properties
	pageTitle="Web Apps 概述 | Azure"
	description="了解 Azure 如何帮助你开发和托管 Web 应用程序"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.date="05/25/2016"
	wacn.date=""/>

# Web Apps 概述

*Azure Web Apps* 是一个完全托管的计算平台，非常适合用来托管网站和 Web 应用程序。Azure 的这个[平台即服务](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) 产品可让你专注于业务逻辑，而让 Azure 负责处理用来运行和缩放应用的基础结构。

下面的 5 分钟视频将介绍 Azure Web Apps。

[AZURE.VIDEO azure-app-service-web-apps-with-yochay-kiriaty]

## Azure 中的 Web 应用是什么？

在 Azure Web 应用中，*Web 应用*是 Azure 提供用来托管网站或 Web 应用程序的计算资源。

该计算资源可能位于共享虚拟机 (VM) 上，也可能位于专用虚拟机上，具体取决于你选择的定价层。你的应用程序代码在独立于其他客户的托管 VM 中运行。

你的代码可以使用 [Azure Web 应用](/documentation/services/web-sites) 支持的任何语言或框架，例如 ASP.NET、Node.js、Java、PHP 或 Python。你也可以在 Web 应用中运行使用 [PowerShell 和其他脚本语言](/documentation/articles/web-sites-create-web-jobs#acceptablefiles)的脚本。

有关可使用 Web Apps 的典型应用程序方案的示例，请参阅 [Web 应用方案](https://azure.microsoft.com/documentation/scenarios/web-app/)以及 [Azure Web 应用、虚拟机、Service Fabric 和云服务的比较](/documentation/articles/choose-web-site-cloud-service-vm#scenarios)的**方案和建议**一节。

## 为何使用 Web Apps？

以下是 Azure 适用于 Web Apps 的一些主要功能：

- **多种语言和框架** — Azure 对 ASP.NET、Node.js、Java、PHP 和 Python 提供顶级支持。你也可以在 Azure VM 上运行 [PowerShell 和其他脚本或可执行文件](/documentation/articles/web-sites-create-web-jobs)。

- **DevOps 优化** — 使用 Visual Studio Team Services、GitHub 或 BitBucket 设置[持续集成和部署](/documentation/articles/app-service-continous-deployment)。通过[测试和过渡环境](/documentation/articles/web-sites-staged-publishing)提升更新。执行 [A/B 测试](/documentation/articles/app-service-web-test-in-production-get-start)。使用 [Azure PowerShell](/documentation/articles/powershell-install-configure) 或[跨平台命令行接口 (CLI)](/documentation/articles/xplat-cli-install) 在 Azure 中管理应用。
 
- **具有高可用性的全局缩放** — 以手动或自动方式[增加](/documentation/articles/app-service-scale)或[扩大](/documentation/articles/insights-how-to-scale)。在 Microsoft 全球数据中心基础结构中的任意位置托管应用，Azure [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 将承诺高可用性。

- **到 SaaS 平台和本地数据的连接** — 从适用于企业系统（例如 SAP、Siebel 和 Oracle）的 50 多个[连接器](/documentation/articles/apis-list)、SaaS 服务（例如 Salesforce 和 Office 365）以及 Internet 服务（例如 Facebook 和 Twitter）中进行选择。使用[混合连接](/documentation/articles/integration-hybrid-connection-overview)和 [Azure 虚拟网络](/documentation/articles/web-sites-integrate-with-vnet)访问本地数据。

- **安全性和合规性** — Azure 符合 [ISO、SOC 和 PCI](https://www.microsoft.com/TrustCenter/)。

- **应用程序模板** — 从 [Azure 库](https://azure.microsoft.com/marketplace/)的大量应用程序模板列表中进行选择，以便使用向导来安装流行的开源软件，例如 WordPress、Joomla 和 Drupal。

- **Visual Studio 集成** — Visual Studio 中的专用工具可简化创建、部署和调试工作。

此外，Web 应用可以利用 [API Apps](/documentation/articles/app-service-api-apps-why-best-platform) 提供的功能（例如 CORS 支持）和 [Mobile Apps](/documentation/articles/app-service-mobile-value-prop) 提供的功能（例如推送通知）。有关 Azure Web 应用中应用类型的详细信息，请参阅 [Azure 概述](/documentation/services/web-sites)。

除了 Azure 中的 Web Apps，Azure 还提供可用来托管网站和 Web 应用程序的其他服务。大多数情况下，Web Apps 是最佳选择。对于微服务体系结构，请考虑使用 [Service Fabric](/documentation/services/service-fabric)；如果你需要更好地控制运行代码的 VM，请考虑使用 [Azure 虚拟机](/documentation/services/virtual-machines/)。有关如何在这些 Azure 服务之间做选择的详细信息，请参阅 [Azure Web 应用、虚拟机、Service Fabric 和云服务的比较](/documentation/articles/choose-web-site-cloud-service-vm)。

## 入门

若要开始在 Azure 中向新 Web 应用部署示例代码，请遵循[在 5 分钟内将第一个 Web 应用部署到 Azure](/documentation/articles/app-service-web-get-started) 教程。你需要一个免费 Azure 帐户。

如果想要在注册 Azure 帐户之前开始使用 Azure，请转到[试用 Azure Web 应用](https://tryappservice.azure.com/)，你可以在 Azure 中立即创建一个生存期较短的入门 Web 应用。你不需要使用信用卡，也不需要做出承诺。

<!---HONumber=Mooncake_0627_2016-->