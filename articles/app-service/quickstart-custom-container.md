---
title: 快速入门：在应用服务上运行自定义容器
description: 部署第一个自定义容器即可开始使用 Azure 应用服务中的容器。
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 2aed6a2fea38f10a2e06ea51edb7fb529c8a2dde
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212518"
---
# <a name="run-a-custom-container-in-azure"></a>在 Azure 中运行自定义容器

::: zone pivot="container-windows"
[Azure 应用服务](overview.md)在 Windows 上提供预定义的应用程序堆栈，例如在 IIS 上运行的 ASP.NET 或 Node.js。 预配置的 Windows 容器（预览版）环境锁定了操作系统，使其无法进行管理访问、软件安装、全局程序集缓存更改等操作。 有关详细信息，请参阅 [Azure 应用服务上的操作系统功能](operating-system-functionality.md)。 如果应用程序需要的访问权限超出了预配置环境的允许，则可改为部署自定义 Windows 容器。

本快速入门介绍如何通过 Visual Studio 将 Windows 映像中的 ASP.NET 应用部署到 [Docker Hub](https://hub.docker.com/)。 你将在 Azure 应用服务中的某个自定义容器内运行该应用。

> [!NOTE]
> Windows 上的应用服务容器提供预览版。
>

## <a name="prerequisites"></a>先决条件

为完成此教程：

- <a href="https://hub.docker.com/" target="_blank">注册 Docker 中心帐户</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">安装用于 Windows 的 Docker</a>。
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">将 Docker 切换为运行 Windows 容器</a>。
- <a href="https://www.visualstudio.com/downloads/" target="_blank">安装 Visual Studio 2019</a>，其中包含 **ASP.NET 和 web 开发**以及 **Azure 开发**工作负载。 如果已安装 Visual Studio 2019：

    - 通过选择“帮助” > “检查更新”，在 Visual Studio 中安装最新更新。
    - 在 Visual Studio 中，通过选择“工具” > “获取工具和功能”，添加工作负载。

## <a name="create-an-aspnet-web-app"></a>创建 ASP.NET Web 应用

按照以下步骤创建 ASP.NET Web 应用：

1. 打开 Visual Studio，然后选择“创建新项目”。

1. 在“创建新项目”中，找到并选择用于 C# 的 **ASP.NET Web 应用程序(.NET Framework)** ，然后选择“下一步”。

1. 在“配置新项目”中，将应用程序命名为“myfirstazurewebapp”，然后选择“创建”。

   ![配置 Web 应用项目](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. 可将任何类型的 ASP.NET Web 应用部署到 Azure。 对于本快速入门，请选择“MVC”模板。

1. 选择“Docker 支持”，并确保身份验证设置为“无身份验证”。  选择“创建”。

   ![创建 ASP.NET Web 应用程序](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. 如果 _Dockerfile_ 文件未自动打开，则从解决方案资源管理器中打开。

1. 需要使用[受支持的父映像](#use-a-different-parent-image)。 通过将 `FROM` 行替换为以下代码，更改父映像，并保存文件：

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以在本地运行 Web 应用。 

   ![在本地运行应用](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>发布到 Docker 中心

1. 在解决方案资源管理器中右键单击“myfirstazurewebapp”项目，然后选择“发布”  。

1. 选择“应用服务”，然后选择“发布”。

1. 在“选取发布目标”中选择“容器注册表”和“Docker Hub”，然后单击“发布”。   

   ![从项目概述页发布](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. 提供 Docker Hub 帐户凭据，然后选择“保存”。

   等待部署完成。 “发布”页现在会显示稍后要使用的存储库名称。

   ![从项目概述页发布](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. 复制此存储库名称待以后使用。

## <a name="create-a-windows-container-app"></a>创建 Windows 容器应用

1. 登录 [Azure 门户]( https://portal.azure.com)。

1. 在 Azure 门户的左上角选择“创建资源”。

1. 在 Azure 市场资源列表上方的搜索框中，搜索“用于容器的 Web 应用”并选择“创建” 。

1. 在“Web 应用创建”中，选择你的订阅和一个**资源组**。 如果需要，可以创建新的资源组。

1. 提供应用名称（例如 *win-container-demo*），然后选择“Windows”作为**操作系统**。 在完成时选择“下一步:Docker”以继续。

   ![创建用于容器的 Web 应用。](media/quickstart-custom-container/create-web-app-continer.png)

1. 对于“映像源”，请选择“Docker Hub”；对于“映像和标记”，请输入在[发布到 Docker Hub](#publish-to-docker-hub) 中复制的存储库名称。  

   ![配置用于容器的 Web 应用](media/quickstart-custom-container/configure-web-app-continer.png)

    如果用于 Web 应用程序的自定义映像位于其他位置，例如位于 [Azure 容器注册表](/azure/container-registry/)中或任何其他的专用存储库中，则可在这里对其进行配置。

1. 选择“查看和创建”，然后选择“创建”并等待 Azure 创建所需的资源。 

## <a name="browse-to-the-container-app"></a>浏览到容器应用

Azure 操作完成后，会显示通知框。

![部署成功](media/quickstart-custom-container/portal-create-finished.png)

1. 单击“转到资源”。

1. 在此资源的概述中，单击“URL”旁边的链接。

此时会打开一个如下所示的新浏览器页面：

![Windows 容器应用正在启动](media/quickstart-custom-container/app-starting.png)

等待数分钟，然后再次尝试，直到显示默认 ASP.NET 主页：

![Windows 容器应用正在运行](media/quickstart-custom-container/app-running-vs.png)

祝贺你！ 你正在 Azure 应用服务中运行你的第一个自定义 Windows 容器。

## <a name="see-container-start-up-logs"></a>查看容器启动日志

加载 Windows 容器可能需要一些时间。 若要查看进度，请通过将 \<app_name> 替换为应用的名称，导航到以下 URL。
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

流式传输的日志如下所示：

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>在本地更新并重新部署

1. 在 Visual Studio 的“解决方案资源管理器”中，打开“视图” > “主页” > “Index.cshtml”。   

1. 在顶部附近找到 `<div class="jumbotron">` HTML 标记，将整个元素替换为以下代码：

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. 若要重新部署到 Azure，请在解决方案资源管理器中右键单击“myfirstazurewebapp”项目，然后选择“发布”  。

1. 在发布页上，选择“发布”并等待发布完成。

1. 若要指示应用服务从 Docker 中心拉取新映像，请重启应用。 返回门户中的应用页，单击“重启” > “是”。

   ![在 Azure 中重新启动 Web 应用](./media/quickstart-custom-container/portal-restart-app.png)

再次[浏览到容器应用](#browse-to-the-container-app)。 刷新网页时，应用应会先还原到“正在启动”页，然后在几分钟后再次显示更新的网页。

![已在 Azure 中更新的 Web 应用](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>使用其他父映像

可以任意使用其他自定义的 Docker 映像来运行应用。 但是，必须为所需框架选择适当的[父映像(基础映像)](https://docs.docker.com/develop/develop-images/baseimages/)：

- 若要部署 .NET Framework 应用，请使用基于 Windows Server Core 2019 [Long-Term Servicing Channel (LTSC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 发行版的父映像。 
- 若要部署 .NET Core 应用，请使用基于 Windows Server Nano 1809 [Semi-Annual Servicing Channel (SAC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) 发行版的父映像。 

在应用启动期间，下载父映像需要一些时间。 不过，可以使用已缓存在 Azure 应用服务中的下述父映像之一，缩短启动时间：

- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.7.2-windowsservercore-ltsc2019
- [mcr.microsoft.com/windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/):1809 - 此映像是基础容器，可以跨 Microsoft [ASP.NET Core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) Microsoft Windows Nano Server 映像使用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [迁移到 Azure 中的 Windows 容器](tutorial-custom-container.md)
::: zone-end  

::: zone pivot="container-linux"
Linux 上的应用服务在 Linux 上提供预定义的应用程序堆栈，并支持特定语言，例如 .NET、PHP、Node.js 等。 还可使用自定义 Docker 映像，于尚未在 Azure 中定义的应用程序堆栈中运行 Web 应用。 本快速入门介绍如何将[Azure 容器注册表](/azure/container-registry) (ACR) 中的映像部署到应用服务。

## <a name="prerequisites"></a>先决条件

* 一个 [Azure 帐户](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [适用于 VS Code 的 Azure 应用服务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。 可以使用此扩展在 Azure 平台即服务 (PaaS) 上创建、管理和部署 Linux Web 应用。
* [适用于 VS Code 的 Docker 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)。 可以使用此扩展简化本地 Docker 映像和命令的管理，并将生成的应用映像部署到 Azure。

## <a name="create-an-image"></a>创建映像

若要完成本快速入门，需要在 [Azure 容器注册表](/azure/container-registry)中存储合适的 Web 应用映像。 遵循[快速入门：使用 Azure 门户创建专用容器注册表](/azure/container-registry/container-registry-get-started-portal)中的说明进行操作，但使用 `mcr.microsoft.com/azuredocs/go` 映像而不是 `hello-world` 映像。 若要参考，可[在 Azure 示例存储库中找到示例 Dockerfile](https://github.com/Azure-Samples/go-docs-hello-world)。

> [!IMPORTANT]
> 请确保在创建容器注册表时将“管理员用户”**** 选项设为“启用”****。 也可以在 Azure 门户中从注册表页的“访问密钥”**** 部分进行设置。 应用服务访问需要此设置。

## <a name="sign-in"></a>登录

接下来，使用应用服务扩展启动 VS Code 并登录到 Azure 帐户。 为此，请在活动栏中选择 Azure 徽标，导航到“应用服务”**** 资源管理器，然后选择“登录到 Azure”****，并按照说明进行操作。

![登录到 Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>检查先决条件

现在你可以检查是否已安装并配置了所有必备组件。

在 VS Code 的状态栏中应会显示你的 Azure 电子邮件地址，“应用服务”资源管理器中应会显示你的订阅。****

接下来，验证是否已安装并运行 Docker。 以下命令将显示 Docker 版本（如果它正在运行）。

```bash
docker --version
```

最后，请确保已连接 Azure 容器注册表。 为此，请在活动栏中选择 Docker 徽标，然后导航到“注册表”****。

![注册表](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>将映像部署到 Azure 应用服务

完成所有配置后，即可将映像直接从 Docker 扩展资源管理器部署到 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)。

在 **DOCKER 资源管理器**中的“注册表”**** 节点下找到映像，并将其展开以显示其标记。 右键单击标记，然后选择“将映像部署到 Azure 应用服务”****。

在此处，按照提示选择订阅、全局唯一的应用名称、资源组和应用服务计划。 选择“B1 基本”**** 作为“定价层”，并选择区域。

部署之后，你的应用将在 `http://<app name>.azurewebsites.net` 上可用。

“资源组”**** 是 Azure 中所有应用程序资源的命名集合。 例如，资源组可以包含对网站、数据库和 Azure 函数的引用。

应用服务计划**** 定义用于托管网站的物理资源。 此快速入门在 Linux 基础结构上使用********“基本”托管计划，这意味着该站点将与其他网站一起托管在 Linux 计算机上。 如果开始使用“基本”**** 计划，则可以使用 Azure 门户进行扩展，使你的计算机是在计算机上运行的唯一站点。

## <a name="browse-the-website"></a>浏览网站

“输出”**** 面板将在部署过程中打开，以指示操作的状态。 操作完成后，查找在“应用服务”**** 资源管理器中创建的应用，右键单击该应用，然后选择“浏览网站”**** 以在浏览器中打开该站点。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>后续步骤

祝贺你，你现已成功完成本快速入门！

接下来请查看其他 Azure 扩展。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI 工具](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

或安装 [Azure 工具](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)扩展包获取所有这些工具。

::: zone-end  