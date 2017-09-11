---
title: "针对 Web 应用、移动应用和 API 应用的 Azure 应用服务 | Microsoft Docs"
description: "了解如何借助 Azure 应用服务开发、部署和管理 Web 应用和移动应用。"
keywords: "应用服务, azure 应用服务, 应用服务成本, 缩放, 可缩放, 应用部署, azure 应用部署, paas, 平台即服务, 网站, web, azure 移动"
services: app-service
documentationcenter: 
author: omarkmsft
manager: erikre
editor: cephalin
ms.assetid: 979cafa8-eeb6-4d3b-87cf-764a821c3e4f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/02/2016
ms.author: byvinyal
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a773e43b28b144dd8341b276eee3fa504d4f1080
ms.contentlocale: zh-cn
ms.lasthandoff: 03/21/2017


---
# <a name="what-is-azure-app-service"></a>什么是 Azure 应用服务？
*应用服务* 是 Microsoft Azure 的 [平台即服务](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) 产品。 为任何平台或设备创建 Web 应用和移动应用。 将应用与 SaaS 解决方案集成、与本地应用程序进行连接，以及实现业务流程的自动化。 Azure 在完全托管的虚拟机 (VM) 上运行应用程序，由用户选择共享的 VM 资源或专用 VM。

应用服务所包括的 Web 功能和移动功能是以前作为 Azure 网站和 Azure 移动服务单独交付的。 它还包括各种新功能，可以实现业务流程的自动化，并可托管云 API。 应用服务是一项集成服务，允许用户将各种组件（网站、移动应用后端、RESTful API 和业务流程）组合到单个解决方案中。

下面是一个 4 分钟的视频，简要介绍了应用服务与旧版 Azure 产品/服务的关系，以及其中的新增功能。

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/app-service-history-lesson/player]
> 
> 

## <a name="why-use-app-service"></a>为何使用应用服务？
下面是应用服务的某些主要特性和功能：

* **多种语言和框架** - 应用服务为 ASP.NET、Node.js、Java、PHP 和 Python 提供一流支持。 也可以在应用服务 VM 上运行 [Windows PowerShell 和其他脚本或可执行文件](../app-service-web/web-sites-create-web-jobs.md) 。
* **开发运营优化** - 使用 Visual Studio Team Services、GitHub 或 BitBucket 设置 [持续集成和部署](../app-service-web/app-service-continuous-deployment.md) 。 通过 [测试和过渡环境](../app-service-web/web-sites-staged-publishing.md)提升更新。 执行 [A/B 测试](../app-service-web/app-service-web-test-in-production-get-start.md)。 在应用服务中，利用 [Azure PowerShell](/powershell/azureps-cmdlets-docs) 或[跨平台命令行接口 (CLI)](../cli-install-nodejs.md) 来管理应用。
* **具有高可用性的全局缩放** - 以手动或自动方式进行[增大](../app-service-web/web-sites-scale.md)或[扩大](../monitoring-and-diagnostics/insights-how-to-scale.md)。 在 Microsoft 全球数据中心基础结构中的任意位置托管应用，并且应用服务 [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 承诺高可用性。
* **到 SaaS 平台和本地数据的连接** - 从适用于企业系统（例如 SAP、Siebel 和 Oracle）的 50 多个 [连接器](../connectors/apis-list.md) 、SaaS 服务（例如 Salesforce 和 Office 365）以及 Internet 服务（例如 Facebook 和 Twitter）中进行选择。 使用[混合连接](../biztalk-services/integration-hybrid-connection-overview.md)和 [Azure 虚拟网络](../app-service-web/web-sites-integrate-with-vnet.md)访问本地数据。
* **安全性和合规性** - 应用服务符合 [ISO、SOC 和 PCI](https://www.microsoft.com/TrustCenter/)的要求。
* **应用程序模板** - 从 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 的详尽模板列表中进行选择，使用向导安装流行的开源软件，例如 WordPress、Joomla 和 Drupal。
* **Visual Studio 集成** — Visual Studio 中的专用工具可简化创建、部署和调试工作。

## <a name="app-types-in-app-service"></a>应用服务中的应用类型
应用服务提供多种*应用类型*，每种类型负责托管特定的工作负荷：

* [**Web 应用**](../app-service-web/app-service-web-overview.md) - 负责托管网站和 Web 应用程序。
* [**移动应用**](../app-service-mobile/app-service-mobile-value-prop.md) - 负责托管移动应用后端。
* [**API 应用**](../app-service-api/app-service-api-apps-why-best-platform.md) - 负责托管 RESTful API。
* [**逻辑应用**](../logic-apps/logic-apps-what-are-logic-apps.md) - 用于自动执行业务流程，并跨云集成系统和数据，而无需编写代码。

此处的“应用”一词是指专用于运行工作负荷的托管资源。 以“Web 应用”为例，用户可能习惯于将 Web 应用视为计算资源和应用程序代码，二者共同向浏览器提供功能。 但在应用服务中，*Web 应用*是 Azure 提供的用于托管应用程序代码的计算资源。 

应用程序可能由多个不同类型的应用服务应用组成。 例如，如果应用程序由 Web 前端和 RESTful API 后端组成，则你可以：

- 将该前端和 API 部署到单个 Web 应用  
- 将前端代码部署到 Web 应用，将后端代码部署到 API 应用。 



## <a name="app-service-plans"></a>应用服务计划
[应用服务计划](azure-web-sites-web-hosting-plans-in-depth-overview.md)代表用于托管应用的物理资源的集合。

应用服务计划定义：

- **区域**（美国西部、美国东部，等等）
- **规模计数**（一个、两个、三个实例，等等）
- **实例大小**（小、中、大）
- **SKU**（免费、共享、基本、标准、高级）

分配到**应用服务计划**的所有应用程序将共享它定义的资源，在托管多个应用时可以节省成本。

**应用服务计划**可以从**免费**和**共享** SKU 扩展到**基本**、**标准**和 **高级** SKU，让用户随着不断的发展访问更多的资源和功能。 将应用服务计划设置为**基本**或更高的级别后，还可以控制 VM 的**大小**和规模计数。

应用服务计划的 **SKU** 和**规模**确定成本，而不是托管的应用数目。 

若需更高的可伸缩性并进行更多的网络隔离，可在 [应用服务环境](../app-service-web/app-service-app-service-environment-intro.md)中运行应用。

## <a name="pricing"></a>定价
有关应用服务成本的信息，请参阅 [应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)。

## <a name="test-drive-app-service"></a>体验版应用服务
[创建示例 Web 应用、移动应用或逻辑应用](https://azure.microsoft.com/try/app-service/)，体验它一个小时，无需信用卡，也无需做出承诺，而且毫不费力。

或者打开一个 [免费 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)，试用以下入门教程之一：

* [教程：创建 Web 应用](../app-service-web/app-service-web-get-started.md)
* [教程：创建移动应用](../app-service-mobile/app-service-mobile-android-get-started.md)
* [教程：创建 API 应用](../app-service-api/app-service-api-dotnet-get-started.md)
* [教程：创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)


