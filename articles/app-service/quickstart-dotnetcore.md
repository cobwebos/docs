---
title: 快速入门：创建 C# ASP.NET Core 应用
description: 了解如何通过部署首个 ASP.NET Core 应用，在 Azure 应用服务中运行 Web 应用。
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5604153d31a2828bcefbeccbd2f4fea428e878b5
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961527"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>快速入门：在 Azure 中创建 ASP.NET Core Web 应用

::: zone pivot="platform-windows"  

本快速入门介绍如何创建第一个 ASP.NET Core Web 应用并将其部署到 [Azure 应用服务](overview.md)。 

完成后，你将获得一个 Azure 资源组，其中包含一个应用服务托管计划，以及一个部署了 Web 应用程序的应用服务。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/dotnet/)。
- 本快速入门将应用部署到 Windows 上的应用服务。 若要部署 Linux 上的应用服务，请参阅[在应用服务中创建 .NET Core Web 应用](./quickstart-dotnetcore.md)。
- 安装带有 ASP.NET 和 Web 开发工作负荷的 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>。

  如果已安装 Visual Studio 2019：

  - 通过选择“帮助” > “检查更新”，在 Visual Studio 中安装最新更新。
  - 通过选择“工具” > “获取工具和功能”，添加工作负荷。


## <a name="create-an-aspnet-core-web-app"></a>创建一个 ASP.NET Core Web 应用

遵循以下步骤在 Visual Studio 中创建 ASP.NET Core Web 应用：

1. 打开 Visual Studio 并选择“创建新项目”。

1. 在“创建新项目”中选择“ASP.NET Core Web 应用程序”，确认“C#”列在所选内容的语言中，然后选择“下一步”。

1. 在“配置新项目”中，将 Web 应用程序项目命名为 *myFirstAzureWebApp*，然后选择“创建”。

   ![配置 Web 应用项目](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. 可将任何类型的 ASP.NET Core Web 应用部署到 Azure，但对于本快速入门，请选择“Web 应用程序”模板。 确保“身份验证”设置为“无身份验证”，并且未选择其他选项。 然后选择“创建”。

   ![创建新的 ASP.NET Core Web 应用](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以在本地运行 Web 应用。

   ![Web 应用在本地运行](./media/quickstart-dotnetcore/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>发布 Web 应用

若要发布 Web 应用，必须先创建并配置一个可将应用发布到的新应用服务。 

在设置应用服务的过程中，将会创建：

- 一个新的[资源组](../azure-resource-manager/management/overview.md#terminology)，用于包含该服务的所有 Azure 资源。
- 一个新的[托管计划](./overview-hosting-plans.md)，用于指定托管应用的 Web 服务器场的位置、大小和功能。

遵循以下步骤创建应用服务并发布 Web 应用：

1. 在“解决方案资源管理器”中右键单击“myFirstAzureWebApp”项目，然后选择“发布”。 如果你尚未从 Visual Studio 登录到 Azure 帐户，请选择“添加帐户”或“登录”。 也可以创建免费 Azure 帐户。

1. 在“选择发布目标”对话框中，依次选择“应用服务”、“新建”、“创建配置文件”。

   ![选取发布目标](./media/quickstart-dotnetcore/pick-publish-target-vs2019.png)

1. 在“应用服务:新建”对话框中为应用提供全局唯一的**名称**，可以接受默认名称，也可以输入新名称。 有效字符为：`a-z`、`A-Z`、`0-9` 和 `-`。 此**名称**用作 Web 应用的 URL 前缀，采用 `http://<app_name>.azurewebsites.net` 格式。

1. 对于“订阅”，请接受列出的订阅，或从下拉列表中选择一个新订阅。

1. 在“资源组”中选择“新建”。 在“新资源组名称”中，输入“myResourceGroup”并选择“确定”。 

1. 对于“托管计划”，请选择“新建”。 

1. 在“托管计划:新建”对话框中，输入下表中指定的值：

   | 设置  | 建议的值 | 说明 |
   | -------- | --------------- | ----------- |
   | **托管计划**  | *myFirstAzureWebAppPlan* | 应用服务计划的名称。 |
   | **位置**      | “西欧” | 托管 Web 应用的数据中心。 |
   | **大小**          | *免费* | [定价层](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)确定托管功能。 |
   
   ![创建新的托管计划](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. 将“Application Insights”保留设置为“无”。

1. 在“应用服务:新建”对话框中，选择“创建”开始创建 Azure 资源。

   ![创建新的应用服务](./media/quickstart-dotnetcore/create-new-app-service-vs2019.png)

1. 完成向导中的操作后，选择“发布”。

   ![将 Web 应用发布到 Azure](./media/quickstart-dotnetcore/publish-web-app-vs2019.png)

   Visual Studio 会将 ASP.NET Core Web 应用发布到 Azure，并在默认浏览器中启动该应用。 

   ![已发布 Azure 中运行的 ASP.NET Web 应用](./media/quickstart-dotnetcore/web-app-running-live.png)

**祝贺你！** 你的 ASP.NET Core Web 应用已在 Azure 应用服务中实时运行！

## <a name="update-the-app-and-redeploy"></a>更新应用并重新部署

遵循以下步骤更新并重新部署 Web 应用：

1. 在**解决方案资源管理器**中你的项目下，打开“页” > “Index.cshtml”。

1. 将整个 `<div>` 标记替换为以下代码：

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. 若要重新部署到 Azure，请在“解决方案资源管理器”中右键单击“myFirstAzureWebApp”项目，然后选择“发布”。

1. 在“发布”摘要页中选择“发布” 。

   ![发布对 Web 应用的更新](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png)

发布完成后，Visual Studio 将启动浏览器并转到 Web 应用的 URL。

![已更新 Azure 中运行的 ASP.NET Web 应用](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>管理 Azure 应用

若要管理 Web 应用，请转到 [Azure 门户](https://portal.azure.com)，然后搜索并选择“应用服务”。

![选择应用服务](./media/quickstart-dotnetcore/app-services.png)

在“应用服务”页上，选择 Web 应用的名称。

![在门户中导航到 Azure 应用](./media/quickstart-dotnetcore/select-app-service.png)

Web 应用的“概述”页包含用于基本管理（例如浏览、停止、启动、重启和删除）的选项。 左侧菜单提供用于配置应用的更多页面。

![Azure 门户中的应用服务](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Visual Studio 创建了一个 ASP.NET Core Web 应用，并已将其部署到 Azure 应用服务。

请继续学习下一篇文章，了解如何创建 .NET Core 应用并将其连接到 SQL 数据库：

> [!div class="nextstepaction"]
> [将 ASP.NET Core 与 SQL 数据库配合使用](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [配置 ASP.NET Core 应用](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
[Linux 应用服务](overview.md#app-service-on-linux)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本快速入门演示如何在 Linux 应用服务中创建 [.NET Core](/aspnet/core/) 应用。 使用 [Azure CLI](/cli/azure/get-started-with-azure-cli) 创建应用，并使用 Git 将 .NET Core 代码部署到该应用。

![在 Azure 中运行应用的示例](media/quickstart-dotnetcore/dotnet-browse-azure.png)

可以使用 Mac、Windows 或 Linux 计算机执行本文中的步骤。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本快速入门教程需要：

* <a href="https://git-scm.com/" target="_blank">安装 Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">安装最新的 .NET Core 3.1 SDK</a>

## <a name="create-the-app-locally"></a>在本地创建应用

在计算机的终端窗口中，创建一个名为 `hellodotnetcore` 的目录，并将当前目录切换到该目录。

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

创建新的 .NET Core 应用。

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>在本地运行应用

在本地运行应用程序，以便你能了解将它部署到 Azure 时它的外观应该是什么样的。 

还原 NuGet 包并运行该应用。

```bash
dotnet run
```

打开 Web 浏览器并导航到 `http://localhost:5000` 处的应用。

页面中会显示该示例应用发出的 Hello World  消息。

![使用浏览器进行测试](media/quickstart-dotnetcore/dotnet-browse-local.png)

在终端窗口中，按 **Ctrl+C** 退出 Web 服务器。 为 .NET Core 项目初始化 Git 存储库。

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

浏览到新建的应用。 将 &lt;app name> 替换为你的应用名称  。

```bash
https://<app-name>.azurewebsites.net
```

新应用应该如下所示：

![空应用页](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net:443/&lt;app-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到已部署的应用程序。

```bash
http://<app_name>.azurewebsites.net
```

.NET Core 示例代码在包含内置映像的 Linux 上的应用服务中运行。

![在 Azure 中运行应用的示例](media/quickstart-dotnetcore/dotnet-browse-azure.png)

祝贺你！  现已将第一个 .NET Core 应用部署到 Linux 应用服务。

## <a name="update-and-redeploy-the-code"></a>更新并重新部署代码

在本地目录中，打开 Startup.cs 文件  。 对方法调用 `context.Response.WriteAsync` 中的文本稍作更改：

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

提交在 Git 中所做的更改，然后将代码更改推送到 Azure。

```bash
git commit -am "updated output"
git push azure master
```

完成部署后，切换回在“浏览到应用”  步骤中打开的浏览器窗口，并点击“刷新”。

![已更新的在 Azure 中运行应用的示例](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>管理新的 Azure 应用

转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>管理已创建的应用。

在左侧菜单中单击**应用程序服务**，然后单击 Azure 应用的名称。

![在门户中导航到 Azure 应用](./media/quickstart-dotnetcore/portal-app-service-list.png)

这里我们可以看到应用的“概述”页。 并可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 

![Azure 门户中的应用服务页](media/quickstart-dotnetcore/portal-app-overview.png)

左侧菜单提供了用于配置应用的不同页面。 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：将 ASP.NET Core 应用与 SQL 数据库配合使用](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [配置 ASP.NET Core 应用](configure-language-dotnetcore.md)

::: zone-end