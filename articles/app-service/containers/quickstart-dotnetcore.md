---
title: 创建 .NET Core Web 应用并将其部署到 Linux 应用服务 | Microsoft Docs
description: 在数分钟内将第一个 .NET Core Hello World 应用部署到 Linux 应用服务。
keywords: azure 应用服务、web 应用、dotnet、core、linux、oss
services: app-service
documentationCenter: ''
author: cephalin
manager: syntaxc4
editor: ''
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: quickstart
ms.date: 04/11/2018
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 89973581d7c6aa99ab671d5bd04f258898e9235e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-net-core-web-app-in-app-service-on-linux"></a>在 Linux 应用服务中创建 .NET Core Web 应用

> [!NOTE]
> 本文将应用部署到基于 Linux 的应用服务。 若要部署到基于 _Windows_ 的应用服务，请参阅[在 Azure 中创建 ASP.NET Core Web 应用](../app-service-web-get-started-dotnet.md)。
>

[Linux 应用服务](app-service-linux-intro.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本快速入门演示如何在 Linux 应用服务中创建 [.NET Core](https://docs.microsoft.com/aspnet/core/) 应用。 使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 创建 Web 应用，并使用 Git 将 .NET Core 代码部署到 Web 应用。

![在 Azure 中运行应用的示例](media/quickstart-dotnetcore/dotnet-browse-azure.png)

可以使用 Mac、Windows 或 Linux 计算机执行本文中的步骤。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本快速入门教程：

* <a href="https://git-scm.com/" target="_blank">安装 Git</a>
* <a href="https://www.microsoft.com/net/core/" target="_blank">安装 .NET Core</a>

## <a name="create-the-app-locally"></a>在本地创建应用

在计算机的终端窗口中，创建一个名为 `hellodotnetcore` 的目录，并将当前目录切换到该目录。

```bash
md hellodotnetcore
cd hellodotnetcore
```

新建一个 .NET Core Web 应用。

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>在本地运行应用

还原 NuGet 包并运行该应用。

```bash
dotnet restore
dotnet run
```

打开 Web 浏览器并导航到 `http://localhost:5000` 处的应用。

页面中会显示该示例应用发出的 Hello World 消息。

![使用浏览器进行测试](media/quickstart-dotnetcore/dotnet-browse-local.png)

在终端窗口中，按 **Ctrl+C** 退出 Web 服务器。 为 .NET Core 项目初始化 Git 存储库。

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

浏览到新建的 Web 应用。 将 _&lt;应用名称>_ 替换为 Web 应用名称。

```bash
http://<app name>.azurewebsites.net
```

新 Web 应用应该如下所示：

![空 Web 应用页面](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://cephalin-dotnetcore.scm.azurewebsites.net/cephalin-dotnetcore.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到已部署的应用程序。

```bash
http://<app_name>.azurewebsites.net
```

.NET Core 示例代码在包含内置映像的 Web 应用中运行。

![在 Azure 中运行应用的示例](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**祝贺你！** 现已将第一个 .NET Core 应用部署到 Linux 应用服务。

## <a name="update-and-redeploy-the-code"></a>更新并重新部署代码

在本地目录中，打开 Startup.cs 文件。 对方法调用 `context.Response.WriteAsync` 中的文本稍作更改：

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

提交在 Git 中所做的更改，然后将代码更改推送到 Azure。

```bash
git commit -am "updated output"
git push azure master
```

完成部署后，切换回在“浏览到应用”步骤中打开的浏览器窗口，并点击“刷新”。

![已更新的在 Azure 中运行应用的示例](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-web-app"></a>管理新 Azure Web 应用

转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>管理创建的 Web 应用。

在左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/quickstart-dotnetcore/portal-app-service-list.png)

将看到 Web 应用的概述页。 在此处可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 

![Azure 门户中的应用服务页](media/quickstart-dotnetcore/portal-app-overview.png)

左侧菜单提供用于配置应用的不同页面。 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Linux 上的 Azure 应用服务中生成 .NET Core 和 SQL 数据库 Web 应用](tutorial-dotnetcore-sqldb-app.md)
