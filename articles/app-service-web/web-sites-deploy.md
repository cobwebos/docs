---
title: "将应用部署到 Azure App Service | Microsoft Docs"
description: "了解如何将你的应用部署到 Azure App Service。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: f1464f71-2624-400e-86a2-e687e385804f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin;dariac
translationtype: Human Translation
ms.sourcegitcommit: 283b1cfda82b4f96ad5148c522a4c9833cb4c381
ms.openlocfilehash: 4b3b96e9c5d7a4ff99c803aa356dcb5ad6997978


---
# <a name="deploy-your-app-to-azure-app-service"></a>将应用部署到 Azure App Service
本文可帮助你确定将 Web 应用、移动应用后端或 API 应用的文件部署到 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 的最佳选项，然后将你引导到相应的资源，其中包含特定于你的首选选项的操作说明。

## <a name="a-nameoverviewaazure-app-service-deployment-overview"></a><a name="overview"></a>Azure App Service 部署概述
Azure App Service 保留了应用程序框架（ASP.NET、PHP、Node.js 等等）。 某些框架在默认情况下已启用，而其他框架（如 Java 和 Python）可能需要进行简单的复选标记配置才能启用。 此外，你还可以自定义应用程序框架，如运行时的 PHP 版本或位元。 有关详细信息，请参阅[在 Azure App Service 中配置应用](web-sites-configure.md)。

由于你无需担心 Web 服务器或应用程序框架，因此将应用部署到应用服务只需将代码、二进制文件、内容文件及其各自的目录结构部署到 Azure 中的 [**/site/wwwroot** 目录](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)（对于 Web 作业，部署到 **/site/wwwroot/App_Data/Jobs/** 目录）。 应用服务支持&3; 种不同的部署进程。 本文中的所有部署方法使用以下进程之一： 

* [FTP 或 FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol)：使用你常用的支持 FTP 或 FTPS 的工具（从 [FileZilla](https://filezilla-project.org) 到功能齐全的 IDE，如 [NetBeans](https://netbeans.org)）将文件移至 Azure。 这完全是文件上载进程。 应用服务不提供任何附加服务，例如版本控制、文件结构管理等。 
* [Kudu（Git/Mercurial 或 OneDrive/Dropbox）](https://github.com/projectkudu/kudu/wiki/Deployment)：Kudu 是应用服务中的[部署引擎](https://github.com/projectkudu/kudu/wiki)。 从任何存储库将你的代码直接推送到 Kudu。 只要代码推送到 Kudu，Kudu 还提供附加服务，包括版本控制、程序包还原、MSBuild 和 [Web 挂钩](https://github.com/projectkudu/kudu/wiki/Web-hooks)以用于连续部署和其他自动化任务。 Kudu 部署引擎支持 3 种不同类型的部署源：   
  
  * 从 OneDrive 和 Dropbox 同步内容   
  * 从 GitHub、Bitbucket 和 Visual Studio Team Services 使用自动同步进行基于存储库的连续部署  
  * 从本地 Git 使用手动同步进行基于存储库的部署  
* [Web 部署](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy)：使用自动部署到 IIS 服务器的相同工具，直接从偏好的 Microsoft 工具（例如 Visual Studio）将代码部署到应用服务。 此工具支持仅差异部署、创建数据库、连接字符串转换等操作。Web 部署与 Kudu 的不同之处在于，应用程序二进制文件在部署到 Azure 之前生成。 与 FTP 类似，应用服务不提供任何附加服务。

常用的 Web 开发工具支持其中的一个或多个部署进程。 虽然你选择的工具确定了你可以利用的部署进程，但是由你支配的实际 DevOps 功能取决于部署进程和你选择的特定工具的组合。 例如，如果你从[包含 Azure SDK 的 Visual Studio](#vspros) 执行 Web 部署，即使你未从 Kudu 自动执行，你也会在 Visual Studio 中自动执行程序包还原和 MSBuild。 

> [!NOTE]
> 这些部署过程并不会真正[预配应用可能需要的 Azure 资源](../azure-resource-manager/resource-group-template-deploy-portal.md)。 但是，大多数链接的操作方法文章会向你展示如何预配应用并端到端地将代码部署到该应用。 你还可以在[使用命令行工具自动部署](#automate)部分中找到用于预配 Azure 资源的其他选项。
> 
> 

## <a name="a-nameftpadeploy-manually-by-uploading-files-with-ftp"></a><a name="ftp"></a>通过 FTP 上传文件进行手动部署
如果你习惯于手动将 Web 内容复制到 Web 服务器，可以使用 [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) 实用工具（如 Windows 资源管理器或 [FileZilla](https://filezilla-project.org/)）复制文件。

手动复制文件的优点是：

* FTP 工具使用顺手且复杂性很低。 
* 完全知道文件要复制到何处。
* 使用 FTPS 可增加安全性。

手动复制文件的缺点是：

* 必须知道如何将文件部署到应用服务中的正确目录。 
* 发生故障时没有针对回退的版本控制。
* 无法提供用于排查部署问题的内置部署历史记录。
* 部署时间可能很长，因为许多 FTP 工具不提供仅差异复制，而只是复制所有文件。  

### <a name="a-namehowtoftpahow-to-upload-files-with-ftp"></a><a name="howtoftp"></a>如何使用 FTP 上传文件
[Azure 门户](https://portal.azure.com)中提供了使用 FTP 或 FTPS 连接到应用目录所需的全部信息。

* [使用 FTP 将应用部署到 Azure 应用服务](app-service-deploy-ftp.md)

## <a name="a-namedropboxadeploy-by-syncing-with-a-cloud-folder"></a><a name="dropbox"></a>通过与云文件夹同步进行部署
[手动复制文件](#ftp)的一个好的替代方法是从云存储服务（如 OneDrive 和 Dropbox）将文件和文件夹同步到应用服务。 与云文件夹同步利用了 Kudu 部署进程（请参阅[部署过程概述](#overview)）。

与云文件夹同步的优点是：

* 部署简单。 OneDrive 和 Dropbox 等服务提供桌面同步客户端，因此本地工作目录也是你的部署目录。
* 一键式部署。
* Kudu 部署引擎中的所有功能都可用（例如，程序包还原、自动化）。

与云文件夹同步的缺点是：

* 发生故障时没有针对回退的版本控制。
* 没有自动部署，需要手动同步。

### <a name="a-namehowtodropboxahow-to-deploy-by-syncing-with-a-cloud-folder"></a><a name="howtodropbox"></a>如何通过与云文件夹同步进行部署
在 [Azure 门户](https://portal.azure.com)中，可以在 OneDrive 或 Dropbox 云存储中指定一个文件夹进行内容同步，然后使用该文件夹中的应用代码和内容，并通过单击按钮同步到应用服务。

* [将内容从云文件夹同步到 Azure 应用服务](app-service-deploy-content-sync.md)。 

## <a name="a-namecontinuousdeploymentadeploy-continuously-from-a-cloud-based-source-control-service"></a><a name="continuousdeployment"></a>从基于云的源代码管理服务连续部署
如果你的开发团队使用基于云的源代码管理 (SCM) 服务，如 [Visual Studio Team Services](http://www.visualstudio.com/)、[GitHub](https://www.github.com) 或 [BitBucket](https://bitbucket.org/)，则可以将应用服务配置为与你的存储库集成并连续部署。 

从基于云的源代码管理服务部署的优点是：

* 版本控制支持回退。
* 能够为 Git（以及 Mercurial，如果适用）存储库配置连续部署。 
* 分支特定的部署，可以将不同分支部署到不同的[槽](web-sites-staged-publishing.md)。
* Kudu 部署引擎中的所有功能都可用（例如，部署版本控制、回退、程序包还原、自动化）。

从基于云的源代码管理服务部署的缺点是：

* 需要对相关 SCM 服务有一定的了解。

### <a name="a-namevstsahow-to-deploy-continuously-from-a-cloud-based-source-control-service"></a><a name="vsts"></a>如何从基于云的源代码管理服务连续部署
在 [Azure 门户](https://portal.azure.com)中，可以从 GitHub、Bitbucket 和 Visual Studio Team Services 配置连续部署。

* [连续部署到 Azure App Service](app-service-continuous-deployment.md)。 

若要了解如何通过 Azure 门户中未列出的云存储库（如 [GitLab](https://gitlab.com/)）手动配置连续部署，请参阅[使用手动步骤设置连续部署](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

## <a name="a-namelocalgitdeploymentadeploy-from-local-git"></a><a name="localgitdeployment"></a>从本地 Git 部署
如果你的开发团队使用基于 Git 的本地源代码管理 (SCM) 服务，可将它配置为应用服务的部署源。 

从本地 Git 进行部署的优点是：

* 版本控制支持回退。
* 分支特定的部署，可以将不同分支部署到不同的[槽](web-sites-staged-publishing.md)。
* Kudu 部署引擎中的所有功能都可用（例如，部署版本控制、回退、程序包还原、自动化）。

从本地 Git 进行部署的缺点是：

* 需要对相关 SCM 系统有一定的了解。
* 连续部署没有任何现成的解决方案。 

### <a name="a-namevstsahow-to-deploy-from-local-git"></a><a name="vsts"></a>如何从本地 Git 部署
在 [Azure 门户](https://portal.azure.com)中，可以配置本地 Git 部署。

* [从本地 Git 部署到 Azure App Service](app-service-deploy-local-git.md)。 
* [从任何 git/hg 存储库发布到 Web 应用](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)。  

## <a name="deploy-using-an-ide"></a>使用 IDE 进行部署
如果你已在使用包含 [Azure SDK](https://azure.microsoft.com/downloads/) 的 [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) 或其他 IDE 套件（如 [Xcode](https://developer.apple.com/xcode/)、[Eclipse](https://www.eclipse.org) 和 [IntelliJ IDEA](https://www.jetbrains.com/idea/)），可以直接从 IDE 内部署到 Azure。 此选项非常适合于单个开发人员。

Visual Studio 支持所有这三种部署过程（FTP、Git 和 Web 部署），具体取决于你的首选项，而其他 IDE 在已集成 FTP 或 Git 时可部署到应用服务（请参阅[部署过程概述](#overview)）。

使用 IDE 进行部署的优点是：

* 可最大程度减少在端到端应用程序生命周期中使用工具。 开发、调试、跟踪你的应用以及将其部署到 Azure，所有这些操作都在 IDE 内部执行。 

使用 IDE 进行部署的缺点是：

* 增加了工具使用方面的复杂性。
* 团队项目仍需要源代码管理系统。

<a name="vspros"></a>使用包含 Azure SDK 的 Visual Studio 进行部署的其他优点是：

* Azure SDK 使 Azure 资源在 Visual Studio 中处于第一等级。 创建、删除、编辑、启动和停止应用、查询后端 SQL 数据库、实时调试 Azure 应用，以及执行更多操作。 
* 实时编辑 Azure 上的代码文件。
* 实时调试 Azure 上的应用。
* 已集成 Azure 资源管理器。
* 允许进行仅差异部署。 

### <a name="a-namevsahow-to-deploy-from-visual-studio-directly"></a><a name="vs"></a>如何直接从 Visual Studio 部署
* [Azure 和 ASP.NET 入门](web-sites-dotnet-get-started.md)。 如何使用 Visual Studio 和 Web 部署来创建和部署一个简单的 ASP.NET MVC Web 项目。
* [如何使用 Visual Studio 部署 Azure Web 作业](websites-dotnet-deploy-webjobs.md)。 如何配置控制台应用程序项目，以便将它们部署为 Web 作业。  
* [将包含成员资格、OAuth 和 SQL 数据库的安全 ASP.NET MVC 5 应用部署到 Web 应用](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。 如何使用 Visual Studio、Web 部署和 Entity Framework Code First 迁移通过 SQL 数据库来创建和部署 ASP.NET MVC Web 项目。
* [使用 Visual Studio 的 ASP.NET Web 部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)。 共 12 篇的系列教程涵盖了比此列表中其他部署任务更完整的部署任务。 自编写本教程以来添加了一些 Azure 部署功能，但注释是后来添加的，说明缺少哪些内容。
* [在 Visual Studio 2012 中直接从 Git 存储库将 ASP.NET 网站部署到 Azure](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)。 说明如何在 Visual Studio 中部署 ASP.NET Web 项目（使用 Git 插件将代码提交到 Git 并将 Azure 连接到 Git 存储库）。 从 Visual Studio 2013 开始，Git 支持是内置的，不需要安装插件。

### <a name="a-nameaztkahow-to-deploy-using-the-azure-toolkits-for-eclipse-and-intellij-idea"></a><a name="aztk"></a>如何使用 Azure Toolkits for Eclipse 和 Azure Toolkits for IntelliJ IDEA 进行部署
Microsoft 提供 [Azure Toolkit for Eclipse](../azure-toolkit-for-eclipse.md) 和 [Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij.md)，可用于将 Web 应用直接从 Eclipse 和 IntelliJ 部署到 Azure。 下面的教程演示如何使用任一 IDE 将简单的“Hello”world Web 应用部署到 Azure：

* [在 Eclipse 中创建 Azure 的 Hello World Web 应用](app-service-web-eclipse-create-hello-world-web-app.md)。 本教程说明如何使用 Azure Toolkit for Eclipse 创建和部署 Azure 的 Hello World Web 应用。
* [在 IntelliJ 中创建 Azure 的 Hello World Web 应用](app-service-web-intellij-create-hello-world-web-app.md)。 本教程说明如何使用 Azure Toolkit for IntelliJ 创建和部署 Azure 的 Hello World Web 应用。

## <a name="a-nameautomateaautomate-deployment-by-using-command-line-tools"></a><a name="automate"></a>使用命令行工具自动部署
若要选择命令行终端作为开发环境，可使用命令行工具对应用服务应用编写部署任务脚本。 

使用命令行工具进行部署的优点是：

* 允许已编写脚本的部署方案。
* 将 Azure 资源预配和代码部署集成。
* 将 Azure 部署集成到现有的持续集成脚本。

使用命令行工具进行部署的缺点是：

* 不适用于首选 GUI 的开发人员。

### <a name="a-nameautomatehowahow-to-automate-deployment-with-command-line-tools"></a><a name="automatehow"></a>如何使用命令行工具进行自动部署

有关命令行工具的列表和教程链接，请参阅[使用命令行工具自动部署 Azure 应用](app-service-deploy-command-line.md)。 

## <a name="a-namenextstepsanext-steps"></a><a name="nextsteps"></a>后续步骤
在某些情况下，你可能想要能够轻松地在应用的过渡版本和生产版本之间来回切换。 有关详细信息，请参阅 [Web 应用上的过渡部署](web-sites-staged-publishing.md)。

准备好备份和还原计划是任何部署工作流的一个重要部分。 有关应用服务备份和还原功能的信息，请参阅 [Web 应用备份](web-sites-backup.md)。  

有关如何使用 Azure 的基于角色的访问控制来管理应用服务部署访问权限的信息，请参阅 [RBAC 和 Web 应用发布](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/)。




<!--HONumber=Jan17_HO1-->


