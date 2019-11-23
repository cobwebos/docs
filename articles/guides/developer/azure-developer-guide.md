---
title: 面向 Azure 开发人员的入门指南 | Microsoft 文档
description: This article provides essential information for developers looking to get started using the Microsoft Azure platform for their development needs.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: c8ab954471e597cfea5c6f56cd45b2191aa73242
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424082"
---
# <a name="get-started-guide-for-azure-developers"></a>获取适用于 Azure 开发人员的入门指南

## <a name="what-is-azure"></a>什么是 Azure？

Azure is a complete cloud platform that can host your existing applications and streamline new application development. Azure can even enhance on-premises applications. Azure integrates the cloud services that you need to develop, test, deploy, and manage your applications, all while taking advantage of the efficiencies of cloud computing.

通过在 Azure 中托管应用程序，你可以随着客户需求的增长，从小规模开始轻松扩展应用程序。 另外，Azure 还可以针对高可用性应用程序提供所需的可靠性，甚至包括在两个不同区域之间的故障转移。 通过 [Azure 门户](https://portal.azure.com)，可让你轻松管理所有的 Azure 服务。 同时，你还可以通过使用特定于服务的 API 和模板以编程方式管理你的服务。

本指南介绍面向应用程序开发人员的 Azure 平台。 针对在 Azure 中开始生成新的应用程序或将现有应用程序迁移到 Azure 所需的操作，提供相关指导和说明。

## <a name="where-do-i-start"></a>从哪里开始？

With all the services that Azure offers, it can be an intimidating task to figure out which services you need to support your solution architecture. 本部分将重点介绍开发人员通常使用的 Azure 服务。 有关所有 Azure 服务列表，请参阅 [Azure 文档](../../index.md)。

首先，你必须确定在 Azure 中托管应用程序的方式。 是否需要将整个基础结构作为一台虚拟机 (VM) 来管理。 是否可以使用 Azure 提供的平台管理功能？ 或许你只需要一个无服务器框架来托管代码执行？

应用程序需要云存储，Azure 为此提供了若干选项。 你可以利用 Azure 的企业身份验证。 此外，还有用于基于云开发和监视的各种工具，并且大多数托管服务都提供 DevOps 集成。

现在，让我们先看一下我们建议的用于应用程序调查的特定服务。

### <a name="application-hosting"></a>应用程序托管

Azure 提供了多个基于云的计算服务来运行你的应用程序，因此，你无需担心基础结构详细信息。 随着应用程序使用率的增长，可轻松纵向扩展或横向扩展你的资源。

Azure 提供了支持应用程序开发和托管需求的服务。 Azure 还提供了基础结构即服务 (IaaS)，以便让你完全控制应用程序托管。 Azure's Platform as a Service (PaaS) offerings provide the fully managed services needed to power your apps. There's even true serverless hosting in Azure where all you need to do is write your code.

![Azure 应用程序托管选项](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

如果你希望以最快路径发布基于 Web 的项目，则可以考虑使用 Azure 应用服务。 通过应用服务，可以轻松扩展 Web 应用以支持移动客户端，并发布易于使用的 REST API。 此平台通过使用社交提供程序、基于流量的自动缩放、在生产中测试和基于容器的持续部署来提供身份验证。

可以创建 Web 应用、移动应用后端和 API 应用。

由于所有上述三个应用类型均共享应用服务运行时，因此，你可以托管网站，支持移动客户端，并在 Azure 中公开你的 API，所有这些均可在同一个项目或解决方案中完成。 若要了解有关应用服务的详细信息，请参阅[什么是 Azure Web 应用](../../app-service/overview.md)。

应用服务在设计之初就考虑到了 DevOps。 It supports various tools for publishing and continuous integration deployments. These tools include GitHub webhooks, Jenkins, Azure DevOps, TeamCity, and others.

你可以通过使用[联机迁移工具](https://www.migratetoazure.net/)将现有应用程序迁移到应用服务。

> **When to use**: Use App Service when you’re migrating existing web applications to Azure, and when you need a fully-managed hosting platform for your web apps. 另外，当需要支持移动客户端或者使用你的应用公开 REST API时，也可以使用应用服务。
> 
> **开始使用**：通过应用服务，可以轻松创建和部署你的第一个 [Web 应用](../../app-service/app-service-web-get-started-dotnet.md)、[移动应用](../../app-service-mobile/app-service-mobile-ios-get-started.md)，或 [API 应用](../../app-service/app-service-web-tutorial-rest-api.md)。
> 
> **立即试用**：通过应用服务，可以设置一个临时应用来试用该平台，而无需注册 Azure 帐户。 试用平台并[创建 Azure 应用服务应用](https://tryappservice.azure.com/)。

#### <a name="azure-virtual-machines"></a>Azure 虚拟机

作为一个基础结构即服务 (IaaS) 提供程序，Azure 可让你部署到或将应用程序迁移到 Windows 或 Linux VM。 Azure 虚拟机连同 Azure 虚拟网络一起，可支持将 Windows 或 Linux VM 部署到 Azure。 通过 VM，你可以全面控制计算机的配置。 使用 VM 时，你将负责所有服务器软件的安装、配置、维护和操作系统修补。

由于你对 VM 拥有所有控制级别，因此，你可以在 Azure 上运行大量不适合 PaaS 模型的服务器工作负载。 这些工作负载可包括数据库服务器、Windows Server Active Directory 和 Microsoft SharePoint。 有关详细信息，请参阅 [Linux](/azure/virtual-machines/linux/) 或 [Windows](/azure/virtual-machines/windows/) 的虚拟机文档。

> **何时使用**：在你需要完全控制应用程序基础结构，或将本地应用程序工作负载迁移到 Azure 而无需进行更改的情况下，可以使用虚拟机。
> 
> **开始使用**：从 Azure 门户创建 [Linux VM](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) 或 [Windows VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md)。

#### <a name="azure-functions-serverless"></a>Azure Functions（无服务器）

Rather than worrying about building out and managing a whole application or the infrastructure to run your code, what if you could just write your code and have it run in response to events or on a schedule?  [Azure Functions](../../azure-functions/functions-overview.md) 是一个“无服务器”样式的产品，可让你仅编写所需的代码。 With Functions, you can trigger code execution with HTTP requests, webhooks, cloud service events, or on a schedule. 你可以使用所选的开发语言开发编码，如 C\#、F\#、Node.js、Python 或 PHP。 使用基于消耗的计费方式，只需要支付代码执行的时间，并且 Azure 可根据需要进行扩展。

> **何时使用**：在你具有由其他 Azure 服务、基于 Web 的事件，或按计划触发的代码的情况下，可以使用 Azure Functions。 此外，当你不需要完整托管项目的开销或者只想支付代码运行时间时，也可以使用 Functions。 有关详细信息，请参阅 [Azure Functions 概览](../../azure-functions/functions-overview.md)。
> 
> **开始使用**：按照 Functions 快速入门教程，从门户[创建你的第一个函数](../../azure-functions/functions-create-first-azure-function.md)。
> 
> **立即试用**：Azure Functions 可让你运行代码而无需注册 Azure 帐户。 立即试用并[创建你的第一个 Azure Function](https://tryappservice.azure.com/)。

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric is a distributed systems platform. This platform makes it easy to build, package, deploy, and manage scalable and reliable microservices. It also provides comprehensive application management capabilities such as: 

* 提供
* 正在部署 
* 监视
* Upgrading/patching
* 正在删除 

在共享计算机池上运行的应用可以从小规模开始，再根据需要扩展为成百上千个计算机。

Service Fabric 支持具有 Open Web Interface for .NET (OWIN) 和 ASP.NET Core 的 WebAPI。 它提供了用于在 Linux 上使用 .NET Core 和 Java 构建服务的 SDK。 若要了解有关 Service Fabric 的详细信息，请参阅 [Service Fabric 文档](https://docs.microsoft.com/azure/service-fabric/)。

> **何时使用：** 如果要创建应用程序或重新编写现有应用程序，以使用微服务体系结构，则 Service Fabric 将是一个不错的选择。 当你需要更好地控制或直接访问底层基础结构时，也可以使用 Service Fabric。
> 
> **开始使用：** [创建第一个 Azure Service Fabric 应用程序](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md)。

### <a name="enhance-your-applications-with-azure-services"></a>使用 Azure 服务增强应用程序

Along with application hosting, Azure provides service offerings that can enhance the functionality. Azure can also improve the development and maintenance of your applications, both in the cloud and on-premises.

#### <a name="hosted-storage-and-data-access"></a>托管存储和数据访问

Most applications must store data, so however you decide to host your application in Azure, consider one or more of the following storage and data services.

- **Azure Cosmos DB**: A globally distributed, multi-model database service. This database enables you to elastically scale throughput and storage across any number of geographical regions with a comprehensive SLA. 
  
  > **何时使用：** 当应用程序需要文档、表或图形数据库（包括具有多个妥善定义的一致性模型的 MongoDB 数据库）时。 
  > 
  > **开始使用**：[构建 Azure Cosmos DB Web 应用](../../cosmos-db/create-sql-api-dotnet.md)。 如果是 MongoDB 开发人员，请参阅[构建使用 Azure Cosmos DB 的 MongoDB Web 应用](../../cosmos-db/create-mongodb-dotnet.md)。

- Azure 存储：可针对 blob、队列、文件和其他类型非关系数据提供持久且高可用性的存储。 存储服务为 VM 提供了存储基础。

  > **何时使用**：在应用存储非关系数据，如键值对（表）、blob、文件共享，或消息（队列）的情况下使用。
  > 
  > **开始使用**：选择这些类型存储之一：[blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)、[表](../../cosmos-db/table-storage-how-to-use-dotnet.md)、[队列](../../storage/queues/storage-dotnet-how-to-use-queues.md)或[文件](../../storage/files/storage-dotnet-how-to-use-files.md)。

- Azure SQL 数据库：基于 Azure 版本的 Microsoft SQL Server 引擎，用于在云中存储关系表格数据。 SQL 数据库可提供可预测的性能、在不停机情况下进行缩放、业务连续性和数据保护功能。

  > **何时使用**：在应用程序需要具有引用完整性、事务支持和 TSQL 查询支持的数据存储时，可以考虑使用。
  > 
  > **开始使用**：[使用 Azure 门户在几分钟内创建 SQL 数据库](../../sql-database/sql-database-get-started.md)。


你可以使用 [Azure 数据工厂](../../data-factory/introduction.md)将现有本地数据移到 Azure。 如果你还没有准备好将数据移到云中，BizTalk 服务中的[混合连接](../../biztalk-services/integration-hybrid-connection-overview.md)可让你将应用服务托管的应用连接到本地资源。 此外，你还可以从本地应用程序连接到 Azure 数据和存储服务。

#### <a name="docker-support"></a>Docker 支持

Docker 容器是操作系统虚拟化的一种形式，可让你以更高效且可预见的方式部署应用程序。 容器化应用程序在生产环境中的工作方式与在开发和测试系统上的工作方式相同。 你可以通过使用标准的 Docker 工具来管理容器。 可以使用现有技能和常用的开源工具在 Azure 上部署和管理基于容器的应用程序。

Azure 提供了多种方式以便在应用程序中使用容器。

- Azure Docker VM 扩展：允许使用 Docker 工具将虚拟机配置为一个 Docker 主机。

  > **何时使用**：当希望在虚拟机上为应用程序生成一致的容器部署，或者当想要使用 [Docker Compose](https://docs.docker.com/compose/overview/) 的情况下使用。
  > 
  > **开始使用**：[在 Azure 中使用 Docker VM 扩展创建 Docker 环境](../../virtual-machines/virtual-machines-linux-dockerextension.md)。

- Azure 容器服务：允许你创建、配置和管理预配置的、以运行容器化应用程序的虚拟机群集。 若要了解有关容器服务的详细信息，请参阅 [Azure 容器服务简介](../../container-service/container-service-intro.md)。

  > **何时使用**：当需要构建提供其他计划和管理工具的生产就绪、可缩放环境，或者当部署 Docker Swarm 群集时使用。
  > 
  > **开始使用**：[部署容器服务群集](../../container-service/dcos-swarm/container-service-deployment.md)。

- Docker 计算机：允许使用 docker 计算机命令安装和管理虚拟主机上的 Docker 引擎。

  >**何时使用**：当需要通过创建一个 Docker 主机快速原型化应用时使用。

- 应用服务的自定义 Docker 映像：在 Linux 上部署 Web 应用时，可以使用容器注册表或客户容器中的 Docker 容器。

  > **何时使用**：在 Linux 上将 Web 应用部署到 Docker 映像时使用。
  > 
  > **开始使用**：[在 Linux 上使用应用服务的自定义 Docker 映像](../../app-service/containers/quickstart-docker-go.md)。

### <a name="authentication"></a>Authentication

重要的是：不仅要知道谁在使用你的应用程序，而且还要防止对资源的未授权访问。 Azure 提供了多种方式来对你的应用客户端进行身份验证。

- Azure Active Directory (Azure AD)：Microsoft 基于云的多租户标识和访问管理服务。 你可以通过与 Azure AD 集成将单一登录 (SSO) 添加到你的应用程序。 可以通过直接使用 Azure AD Graph API 或 Microsoft Graph API 访问目录属性。 可以通过使用本机 HTTP/REST 终结点和多平台 Azure AD 身份验证库与支持 OAuth2.0 授权框架的 Azure AD 和 Open ID Connect 相集成。

  > **何时使用**：当想要提供 SSO 体验、使用基于图形的数据，或者对基于域的用户进行身份验证时使用。
  > 
  > **开始使用**：若要了解详细信息，请参阅 [Azure Active Directory 开发人员指南](../../active-directory/develop/v1-overview.md)。

- 应用服务身份验证：当选择应用服务来托管应用时，还将获得对 Azure AD 以及社交标识提供程序（包括 Facebook、Google、Microsoft 和 Twitter）的内置身份验证支持。

  > **何时使用**：当想要通过使用 Azure AD、社交标识提供程序或两者来启用应用服务应用中的身份验证时使用。
  > 
  > **开始使用**：若要了解有关应用服务中身份验证的详细信息，请参阅 [Azure 应用服务中的身份验证和授权](../../app-service/overview-authentication-authorization.md)。

若要了解有关 Azure 中安全最佳实践的详细信息，请参阅 [Azure 安全最佳实践和模式](../../security/fundamentals/best-practices-and-patterns.md)。

### <a name="monitoring"></a>监视

With your application up and running in Azure, you need to monitor performance, watch for issues, and see how customers are using your app. 为此，Azure 提供了几个监视选项。

-   Visual Studio Application Insights：与 Visual Studio 集成以监视实时 Web 应用的 Azure 托管的可扩展分析服务。 It gives you the data that you need to improve the performance and usability of your apps continuously. This improvement occurs whether you host your applications on Azure or not.

    >**开始使用**：按照 [Application Insights 教程](../../azure-monitor/app/app-insights-overview.md)。

-   **Azure Monitor**: A service that helps you to visualize, query, route, archive, and act on the metrics and logs that you generate with your Azure infrastructure and resources. Monitor is a single source for monitoring Azure resources and provides the data views that you see in the Azure portal.
 
    >**开始使用**：[Azure Monitor 入门](../../monitoring-and-diagnostics/monitoring-get-started.md)。

### <a name="devops-integration"></a>DevOps 集成

无论是预配虚拟机还是发布持续集成的 Web 应用，Azure 都将与大部分常用的 DevOps 工具集成。 You can work with the tools that you already have and maximize your existing experience with support for tools like: 

* Jenkins 
* GitHub 
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **开始使用**：如需查看应用服务应用的 DevOps 选项，请参阅[对 Azure 应用服务的持续部署](../../app-service/deploy-continuous-deployment.md)。
> 
> **立即试用：** [尝试几个 DevOps 集成](https://azure.microsoft.com/try/devops/)。


## <a name="azure-regions"></a>Azure 区域

Azure 是一个全球性云平台，在世界各地的许多区域都可以使用。 When you provision a service, application, or VM in Azure, you're asked to select a region. This region represents a specific datacenter where your application runs or where your data is stored. 这些区域对应于在 [Azure 区域](https://azure.microsoft.com/regions/)页上发布的特定位置。

### <a name="choose-the-best-region-for-your-application-and-data"></a>选择应用程序和数据的最佳区域

使用 Azure 的好处之一是，你可以将应用程序部署到全球范围内的各种数据中心。 你选择的区域可能会影响应用程序的性能。 例如，最好选择更接近于大部分客户的区域，以减少网络请求中的延迟。 You might also want to select your region to meet the legal requirements for distributing your app in certain countries/regions. 最佳的做法是，始终将应用程序数据存储在相同的数据中心，或尽可能靠近托管应用程序的数据中心的数据中心。

### <a name="multi-region-apps"></a>多区域应用

尽管整个数据中心可能会因自然灾害或 Internet 故障等事件而脱机，不过这种情况极少发生。 最佳的做法是，将至关重要的业务应用程序托管在多个数据中心，以提供最大的可用性。 另外，使用多区域也可以减少全球用户的延迟，并在更新应用程序时提供更多的弹性。

某些服务，如虚拟机和应用服务，使用 [Azure 流量管理器](../../traffic-manager/traffic-manager-overview.md)启用在两个区域之间进行故障转移的多区域支持，以支持高可用性的企业应用程序。 有关示例，请参阅 [Azure 参考体系结构：在多个区域中运行 Web 应用程序](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)。

>**何时使用**：当拥有企业和高可用性应用程序受益于故障转移和复制时使用。

## <a name="how-do-i-manage-my-applications-and-projects"></a>如何管理我的应用程序和项目？

Azure 提供了一套丰富的体验，可让你同时以编程方式和在 [Azure 门户](https://portal.azure.com/)中创建并管理你的 Azure 资源、应用程序和项目。

### <a name="command-line-interfaces-and-powershell"></a>命令行接口和 PowerShell

Azure provides two ways to manage your applications and services from the command line. You can use tools like Bash, Terminal, the command prompt, or your command-line tool of choice. Usually, you can do the same tasks from the command line as in the Azure portal—such as creating and configuring virtual machines, virtual networks, web apps, and other services.

-   [Azure 命令行接口 (CLI)](../../xplat-cli-install.md)：允许你连接到 Azure 订阅，并从命令行根据 Azure 资源对各种任务进行编程。

-   [Azure PowerShell](../../powershell-install-configure.md)：提供了一组带 cmdlet 的模块，可让你通过使用 Windows PowerShell 来管理 Azure 资源。

### <a name="azure-portal"></a>Azure 门户

The [Azure portal](https://portal.azure.com) is a web-based application. You can use the Azure portal to create, manage, and remove Azure resources and services. 其中包括：

* A configurable dashboard
* Azure resource management tools
* Access to subscription settings and billing information. 有关详细信息，请参阅 [Azure 门户概述](../../azure-portal-overview.md)。

### <a name="rest-apis"></a>REST API

Azure 是基于一组支持 Azure 门户 UI 的 REST API 构建的。 其中大多数 REST API 还支持通过编程方式设置和管理来自任何启用 Internet 设备的 Azure 资源和应用程序。 有关完整的 REST API 文档集，请参阅 [Azure REST SDK 参考](https://docs.microsoft.com/rest/api/)。

### <a name="apis"></a>API

Along with REST APIs, many Azure services also let you programmatically manage resources from your applications by using platform-specific Azure SDKs, including SDKs for the following development platforms:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

[移动应用](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md)和 [Azure 媒体服务](../../media-services/previous/media-services-dotnet-how-to-use.md)等服务均提供客户端 SDK，可让你从 Web 和移动客户端应用访问服务。

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Running your app on Azure likely involves working with multiple Azure services. These services follow the same life cycle and can be thought of as a logical unit. 例如，Web 应用可以使用 Web 应用、SQL 数据库、存储、Azure Redis 缓存，以及 Azure 内容分发网络服务。 [Azure 资源管理器](../../azure-resource-manager/resource-group-overview.md)能够使你以组的方式处理应用程序中的资源。 你可以通过单个协调的操作来部署、更新或删除所有资源。

Along with logically grouping and managing related resources, Azure Resource Manager includes deployment capabilities that let you customize the deployment and configuration of related resources. For example, you can use Resource Manager deploy and configure an application. This application can consist of multiple virtual machines, a load balancer, and an Azure SQL database as a single unit.

可使用 Azure 资源管理器模板（JSON 格式的文档）来开发这些部署。 借助这些模板，可使用声明性模板（而不是脚本）来定义应用程序的部署和管理。 模板可用于测试、暂存和生产等不同的环境。 For example, you can use templates to add a button to a GitHub repo that deploys the code in the repo to a set of Azure services with a single click.

> **何时使用**：在希望对通过使用 REST API、Azure CLI 和 Azure PowerShell 以编程方式进行管理的应用执行基于模板的部署时，可以使用资源管理器模板。
> 
> **开始使用**：若要开始使用模板，请参阅[创作 Azure 资源管理器模板](../../resource-group-authoring-templates.md)。

## <a name="understanding-accounts-subscriptions-and-billing"></a>了解帐户、订阅和计费

作为开发人员，我们都喜欢直接深入了解代码，并尽快开始运行我们的应用程序。 当然，我们也非常希望你尽可能轻松地在 Azure 中开始工作。 为了简化这一过程，Azure 提供了[免费试用版](https://azure.microsoft.com/free/)。 某些服务还具有“免费试用”功能，如 [Azure 应用服务](https://tryappservice.azure.com/)，甚至不需要你创建帐户。 As fun as it is to dive into coding and deploying your application to Azure, it's also important to take some time to understand how Azure works. Specifically,  you should understand how it works from a standpoint of user accounts, subscriptions, and billing.

### <a name="what-is-an-azure-account"></a>什么是 Azure 帐户？

To create or work with an Azure subscription, you must have an Azure account. An Azure account is simply an identity in Azure AD or in a directory, such as a work or school organization, that Azure AD trusts. 如果你不属于此类组织，则始终可以通过使用受 Azure AD 信任的 Microsoft 帐户来创建订阅。 若要了解有关将本地 Windows Server Active Directory 与 Azure AD 集成的详细信息，请参阅[将本地标识与 Azure Active Directory 集成](../../active-directory/hybrid/whatis-hybrid-identity.md)。

每个 Azure 订阅都与某个 Azure AD 实例存在信任关系。 这意味着，此订阅信任该目录对用户、服务和设备执行身份验证。 多个订阅可以信任同一个目录，但一个订阅只能信任一个目录。 有关详细信息，请参阅 [Azure 订阅与 Azure Active Directory 的关联方式](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

As well as defining individual Azure account identities, also called *users*, you can define *groups* in Azure AD. 创建用户组是通过使用基于角色的访问控制 (RBAC) 来管理订阅中资源的一种好方式。 若要了解如何创建组，请参阅[在 Azure Active Directory 预览版中创建组](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 此外，也可以通过[使用 PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md) 创建和管理组。

### <a name="manage-your-subscriptions"></a>管理订阅

订阅是链接到 Azure 帐户的 Azure 服务的逻辑分组。 一个 Azure 帐户可包含多个订阅。 Azure 服务按订阅计费。 对于按类型提供的可用订阅优惠列表，请参阅 [Microsoft Azure 优惠详细信息](https://azure.microsoft.com/support/legal/offer-details/)。 Azure subscriptions have an Account Administrator who has full control over the subscription. They also have a Service Administrator who has control over all services in the subscription. 有关经典订阅管理员的信息，请参阅[添加或更改 Azure 订阅管理员](../../billing/billing-add-change-azure-subscription-administrator.md)。 Individual accounts can be granted detailed control of Azure resources using [role-based access control (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>资源组

预配新的 Azure 服务时，可在给定订阅中执行此操作。 各个 Azure 服务（也称为资源）在资源组的上下文中创建。 通过资源组，可更加轻松地部署和管理应用程序的资源。 资源组应包含要作为一个单元使用的应用程序的所有资源。 可在资源组之间移动资源，甚至可将资源移到不同的订阅。 若要了解有关移动资源的信息，请参阅[将资源移到新资源组或订阅](../../resource-group-move-resources.md)。

Azure 资源浏览器是一款非常出色的工具，可用于可视化已在订阅中创建的资源。 若要了解详细信息，请参阅[使用 Azure 资源浏览器查看和修改资源](../../resource-manager-resource-explorer.md)。

#### <a name="grant-access-to-resources"></a>授予对资源的访问权限

When you allow access to Azure resources, it’s always a best practice to provide users with the least privilege that’s required to do a given task.

- **基于角色的访问控制 (RBAC)** ：在 Azure 中，可以在如下指定范围内授予对用户帐户（主体）的访问权限：订阅、资源组或单个资源。 RBAC lets you deploy resources into a resource group and grant permissions to a specific user or group. It also lets you limit access to only the resources that belong to the target resource group. 此外，还可以授予对单个资源的访问权限，例如虚拟机或虚拟网络。 若要授予访问权限，请将角色分配给用户、组或服务主体。 预定义角色有很多，但也可定义自己的自定义角色。 有关详细信息，请参阅[什么是基于角色的访问控制 (RBAC)？](../../role-based-access-control/overview.md)。

  > 何时使用：当需要对用户和组进行细致的访问管理，或者当需要使用户成为订阅的所有者时使用。
  > 
  > 开始使用：有关详细信息，请参阅[使用 RBAC 和 Azure 门户管理访问权限](../../role-based-access-control/role-assignments-portal.md)。

- **Service principal objects**: Along with providing access to user principals and groups, you can grant the same access to a service principal.

  > **何时使用**：当你以编程方式管理 Azure 资源或授予应用程序访问权限时。 有关详细信息，请参阅[创建 Active Directory 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。

#### <a name="tags"></a>Tags

通过 Azure 资源管理器，你可以将自定义标记分配给单个资源。 标记为键值对，在出于计费或监视目的而需要组织资源时，标记可能会十分有用。 标记提供了一种跨多个资源组跟踪的资源方法。 You can assign tags the following ways:

* In the portal 
* In the Azure Resource Manager template 
* 使用 REST API
* 使用 Azure CLI
* 使用 PowerShell 

你可以对每个资源分配多个标记。 若要了解详细信息，请参阅[使用标记来组织 Azure 资源](../../resource-group-using-tags.md)。

### <a name="billing"></a>计费

在从本地计算移动到云托管服务的过程中，对服务使用情况以及相关的成本进行跟踪和估计都是非常重要的问题。 It’s important to estimate what new resources cost to run on a monthly basis. You can also project how the billing looks for a given month based on the current spending.

#### <a name="get-resource-usage-data"></a>获取资源使用情况数据

Azure 提供了一组允许访问资源消耗和 Azure 订阅的元数据信息的计费 REST API。 使用这些计费 API，可让你能够更好地预测和管理 Azure 费用。 You can track and analyze spending in hourly increments and create spending alerts. You can also predict future billing based on current usage trends.

>**开始使用**：若要了解有关使用计费 API 的详细信息，请参阅 [Azure 计费使用情况和 RateCard API 概述](../../billing-usage-rate-card-overview.md)。

#### <a name="predict-future-costs"></a>预测未来成本

Although it's challenging to estimate costs ahead of time, Azure has tools that can help. It has a [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to help estimate the cost of deployed resources. You can also use the Billing resources in the portal and the Billing REST APIs to estimate future costs, based on current consumption.

>**开始使用**：请参阅 [Azure 计费使用情况和 RateCard API 概述](../../billing-usage-rate-card-overview.md)。
