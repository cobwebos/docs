---
title: "在 Visual Studio Code 中创建 ASP.NET Core Web 应用"
description: "本教程演示了如何使用 Visual Studio Code 创建 ASP.NET Core Web 应用。"
services: app-service\web
documentationcenter: .net
author: erikre
manager: erikre
editor: jimbe
ms.assetid: 877bff08-9ef7-405a-a1ca-1194f33c55f2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 46e3852dc84265de41bb358f482dec06608e7efa
ms.contentlocale: zh-cn
ms.lasthandoff: 05/08/2017


---
# <a name="create-an-aspnet-core-web-app-in-visual-studio-code"></a>在 Visual Studio Code 中创建 ASP.NET Core Web 应用
## <a name="overview"></a>概述
本教程演示如何使用 [Visual Studio Code (VS Code)](http://code.visualstudio.com//Docs/whyvscode) 创建 ASP.NET Core Web 应用，并将其部署到 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md)。 

> [!NOTE]
> 尽管本文中指的是 Web 应用，但内容同样适用于 API 应用和移动应用。 
> 
> 

ASP.NET Core 是大幅重新设计的 ASP.NET。 ASP.NET Core 是新的开源跨平台框架，用于通过 .NET 构建基于云的现代 Web 应用。 有关详细信息，请参阅 [Introduction to ASP.NET Core](http://docs.asp.net/latest/conceptual-overview/aspnet.html)（ASP.NET Core 简介）。 有关 Azure 应用服务 Web 应用的信息，请参阅 [Web 应用概述](app-service-web-overview.md)。

[!INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## <a name="prerequisites"></a>先决条件
* 安装 [VS Code](http://code.visualstudio.com/Docs/setup)。
* 安装 Git - 可以从以下位置安装：[Chocolatey](https://chocolatey.org/packages/git) 或 [git-scm.com](http://git-scm.com/downloads)。 如果你是 Git 新手，请选择 [git-scm.com](http://git-scm.com/downloads)，并选择“从 Windows 命令提示符使用 Git”的选项。 安装 Git 后，还需要设置 Git 用户名和电子邮件，因为本教程稍后需要用到（从 VS Code 执行提交时）。  

## <a name="install-aspnet-core"></a>安装 ASP.NET Core
ASP.NET Core 是精简的 .NET 堆栈，用于构建在 OS X、Linux 和 Windows 上运行的现代云应用及 Web 应用。 它已从头开始构建，可将优化的开发框架提供给已部署到云或本地运行的应用。 它由系统开销最低的模块化组件构成，因此你可以在构建解决方案时保持弹性。

本教程旨在帮助你使用最新开发版本的 ASP.NET Core 开始构建应用程序。 以下说明特定于 Windows。 有关 OS X、Linux 和 Windows 的安装说明，请参阅 [Getting Started with ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started)（ASP.NET Core 入门）。 


> [!NOTE]
> 有关 OS X、 Linux 和 Windows 的更详细安装说明，请参阅[安装 ASP.NET Core](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)。 
> 
> 

## <a name="create-the-web-app"></a>创建 Web 应用
本部分说明如何使用 .NET CLI 工具创建新应用 ASP.NET Web 应用的基架。 

1. 在命令提示符下输入以下命令，以创建项目文件夹，并创建应用的基架。
   
```terminal
mkdir SampleWebApp
cd SampleWebApp
dotnet new mvc
```
![dotnet CLI - ASP.NET Core 生成器](./media/web-sites-create-web-app-using-vscode/dotnetcore-mvc-01.png)

2. 若要还原所需的 NuGet 包，请运行以下命令：
   
    ```terminal
    dotnet restore
    ```

## <a name="run-the-web-app-locally"></a>在本地运行 Web 应用
在创建 Web 应用并检索应用的所有 NuGet 包后，你可以在本地运行该 Web 应用。

1. 运行应用（过期时 `dotnet run` 命令将生成应用）：
    ```terminal
    dotnet run
    ```
2. 打开浏览器并导航到以下 URL。
   
    **http://localhost:5000**
   
    将显示 Web 应用的默认页面，如下所示。
   
    ![浏览器中的本地 Web 应用](./media/web-sites-create-web-app-using-vscode/08-web-app.png)
3. 关闭浏览器。 在**命令窗口**中，按 **Ctrl+C** 关闭应用程序并关闭**命令窗口**。 

## <a name="create-a-web-app-in-the-azure-portal"></a>在 Azure 门户中创建 Web 应用
以下步骤将指导用户在 Azure 门户中创建 Web 应用。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击门户左上角的“新建”。
3. 单击“Web 应用”>“Web 应用”。
   
    ![新的 Azure Web 应用](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)
4. 输入“名称”的值，例如 **SampleWebAppDemo**。 请注意，此名称必须是唯一的；当你尝试输入名称时，门户会强制此要求。 因此，如果选择或输入了其他值，需要以该值替换本教程中每个出现的 **SampleWebAppDemo**。 
5. 选择现有的**应用服务计划**或创建新的计划。 如果创建新计划，请选择定价层、位置和其他选项。 有关应用服务计划的详细信息，请参阅 [Azure 应用服务计划深入概述](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)一文。
   
    ![新的 Azure Web 应用边栏选项卡](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)
6. 单击“创建”。
   
    ![Web 应用边栏选项卡](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>为新 Web 应用启用 Git 发布
Git 是分布式版本控制系统，可用于部署 Azure App Service Web 应用。 你将在本地 Git 存储库中存储你为 Web 应用编写的代码，并通过推送到远程存储库将代码部署到 Azure。   

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“浏览”。
3. 单击“Web 应用”，查看与 Azure 订阅关联的 Web 应用的列表。
4. 选择你在本教程中创建的 Web 应用。
5. 在 Web 应用边栏选项卡中，单击“设置” > “连续部署”。 
   
    ![Azure Web 应用主机](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)
6. 单击“选择源”>“本地 Git 存储库”。
7. 单击“确定”。
   
    ![Azure 本地 Git 存储库](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)
8. 如果你事先未设置部署凭据用于发布 Web 应用或其他 App Service 应用，现在请进行设置：
   
   * 单击“设置” > “部署凭据”。 此时将显示“设置部署凭据”边栏选项卡。
   * 创建用户名和密码。  稍后设置 Git 时，将要用到此密码。
   * 单击“保存”。
9. 在 Web 应用的边栏选项卡中，单击“设置”>“属性”。 要部署到的远程 Git 存储库的 URL 将显示在“GIT URL”下。
10. 复制“GIT URL”值以供教程稍后使用。
    
    ![Azure Git URL](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a>将 Web 应用发布到 Azure App Service
在本部分中，你将创建一个本地 Git 存储库，并从该存储库推送到 Azure，以将 Web 应用部署到 Azure。

1. 在 VS Code 的左侧导航栏中，选择“Git”选项。
   
    ![VS Code 中的 Git 图标](./media/web-sites-create-web-app-using-vscode/git-icon.png)
2. 选择“初始化 git 存储库”，确保你的工作区受 git 源代码管理。 
   
    ![初始化 Git](./media/web-sites-create-web-app-using-vscode/19-initgit.png)
3. 打开命令窗口，并切换到 Web 应用的目录。 然后输入以下命令：
   
        git config core.autocrlf false
   
    此命令可防止包含 CRLF 结束符和 LF 结束符的文本出现问题。
4. 在 VS Code 中，添加提交消息，然后单击“全部提交”复选图标。
   
    ![Git 全部提交](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)
5. 完成处理 Git 之后，你会发现，Git 窗口中的“更改”下面未列出任何文件。 
   
    ![Git 无更改](./media/web-sites-create-web-app-using-vscode/no-changes.png)
6. 切换回到命令窗口，其中的命令提示符现在指向 Web 应用所在的目录。
7. 使用前面复制的 Git URL（结尾是“.git”）创建远程引用，以便将更新推送到 Web 应用。
   
        git remote add azure [URL for remote repository]
8. 配置 Git 以在本地保存你的凭据，以便自动将凭据附加到从 VS Code 生成的推送命令。
   
        git config credential.helper store
9. 输入以下命令，将更改推送到 Azure。 在向 Azure 完成初始推送后，你可以从 VS Code 执行所有推送命令。 
   
        git push -u azure master
   
    系统将提示你输入之前在 Azure 中创建的密码。 **注意：密码将不可见。**
   
    上述命令的输出将以部署成功消息结尾。
   
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
        [new branch]      master -> master

> [!NOTE]
> 如果更改了应用，可以通过依次选择“全部提交”选项和“推送”选项，直接在 VS Code 中使用内置 Git 功能重新发布应用。 可以在“全部提交”和“刷新”按钮旁边的下拉菜单中找到可用的“推送”选项。
> 
> 

如果需要与其他人对项目进行协作，应该考虑在推送到 Azure 之前先推送到 GitHub。

## <a name="run-the-app-in-azure"></a>在 Azure 中运行应用
在部署 Web 应用后，让我们运行 Azure 中托管的该应用。 

可以通过两种方法实现此目的：

* 打开浏览器并输入 Web 应用的名称，如下所示。   
  
        http://SampleWebAppDemo.azurewebsites.net
* 在 Azure 门户中，找到 Web 应用的 Web 应用边栏选项卡，然后单击“浏览”以在默认浏览器中 
* 查看应用。

![Azure Web 应用](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## <a name="summary"></a>摘要
在本教程中，你已学习如何在 VS Code 创建 Web 应用并将其部署到 Azure。 有关 VS Code 的详细信息，请参阅[为何使用 Visual Studio Code？](https://code.visualstudio.com/Docs/)一文 若要了解应用服务 Web 应用，请参阅 [Web 应用概述](app-service-web-overview.md)。 


