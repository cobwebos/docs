---
title: 在云中创建 Kubernetes 开发空间 | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 06/06/2018
ms.topic: quickstart
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
manager: douge
ms.openlocfilehash: 3b0e03d47a03411e3e6dc2d073d5087bcb42e03e
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960417"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>快速入门：使用 Azure Dev Spaces 创建 Kubernetes 开发空间（.NET Core 和 Visual Studio）

本指南介绍如何：

- 使用 Azure 中的托管 Kubernetes 群集设置 Azure Dev Spaces。
- 使用 Visual Studio 在容器中以迭代方式开发代码。
- 调试群集中运行的代码。

> [!Note]
> **如果你在任何时候遇到问题**，请参见[故障排除](troubleshooting.md)部分，或在此页上发表评论。 也可以试着学习更详细的[教程](get-started-netcore-visualstudio.md)。

## <a name="prerequisites"></a>先决条件

- 在 EastUS、WestEurope 或 CanadaEast 区域中运行 Kubernetes 1.9.6 且已启用“HTTP 应用程序路由”的 Kubernetes 群集。

  ![请务必启用“Http 应用程序路由”。](media/common/Kubernetes-Create-Cluster-3.PNG)

- Visual Studio 2017（装有“Web 开发”工作负荷）。 如果尚未安装，请在[此处](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)进行下载。

## <a name="set-up-azure-dev-spaces"></a>设置 Azure Dev Spaces

安装[适用于 Azure Dev Spaces 的 Visual Studio 扩展](https://aka.ms/get-azds-visualstudio)。

## <a name="connect-to-a-cluster"></a>连接到群集

接下来，将为 Azure Dev Spaces 创建并配置一个项目。

### <a name="create-an-aspnet-web-app"></a>创建 ASP.NET Web 应用

在 Visual Studio 2017 中创建新项目。 目前，该项目必须是 **ASP.NET Core Web 应用程序**。 将该项目命名为“webfrontend”。

选择“Web 应用程序(模型-视图-控制器)”模板，确保以 **.NET Core** 和 **ASP.NET Core 2.0** 为目标。

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>为 AKS 群集启用 Dev Spaces

使用刚才创建的项目，从启动设置下拉列表中选择“Azure Dev Spaces”，如下所示。

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

在旁边显示的对话框中，确保已使用适当的帐户登录，然后选择一个现有的群集。

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

暂时将“空间”下拉列表保留设置为 `default`。 选中“可公开访问”复选框，允许通过公共终结点访问 Web 应用。

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

单击“确定”，选择或创建群集。

如果选择的群集尚未配置为与 Azure Dev Spaces 一起使用，则会显示一条消息，询问你是否需要对其进行配置。

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

选择“确定”。 

### <a name="look-at-the-files-added-to-project"></a>查看添加到项目的文件
在等待开发空间创建的过程中，可以在选择使用 Azure Dev Spaces 时查看已添加到项目的文件。

- 添加了名为 `charts` 的文件夹，而在该文件夹中，已为应用程序的 [Helm 图表](https://docs.helm.sh)构建了基架。 这些文件用于将应用程序部署到开发空间中。
- `Dockerfile` 提供的信息是以标准 Docker 格式将应用程序打包所必需的。
- `azds.yaml` 包含开发空间所需的开发时配置。

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>在 Kubernetes 中调试容器
成功创建开发空间之后，即可调试应用程序。 在代码中设置一个断点，例如在设置 `Message` 变量时所在的 `HomeController.cs` 文件的第 20 行处设置断点。 单击“F5”开始调试。 

Visual Studio 会与生成和部署应用程序所需的开发空间通信，然后打开有 Web 应用运行的浏览器。 容器看起来像是在本地运行，但实际上是在 Azure 的开发空间中运行。 使用 localhost 地址的原因是 Azure Dev Spaces 会创建一个通往在 AKS 中运行的容器的临时 SSH 隧道。

单击页面顶部的“关于”链接即可触发断点。 可以不受限制地访问调试信息（例如调用堆栈、局部变量、异常信息等），就像在本地执行代码一样。


## <a name="iteratively-develop-code"></a>以迭代方式开发代码

Azure Dev Spaces 不仅仅是用来让代码在 Kubernetes 中运行，它还可以用来快速地以迭代方式查看所做的代码更改在云的 Kubernetes 环境中的效果。

### <a name="update-a-content-file"></a>更新内容文件
1. 找到 `./Views/Home/Index.cshtml` 文件，对 HTML 进行编辑。 例如，将第 70 行的 `<h2>Application uses</h2>` 更改为类似 `<h2>Hello k8s in Azure!</h2>` 的内容
1. 保存文件。
1. 转到浏览器并刷新页面。 此时会看到网页显示更新的 HTML。

发生了什么情况？ 对内容文件（例如 HTML 和 CSS）所做的编辑不需要在 .NET Core Web 应用中进行重新编译，因此，活动的 F5 会话会自动将任何修改的内容文件同步到 AKS 中正在运行的容器，方便你立即查看所做的内容编辑。

### <a name="update-a-code-file"></a>更新代码文件
更新代码文件需要的工作多一些，因为 .NET Core 应用需重新构建并生成更新的应用程序二进制文件。

1. 停止 Visual Studio 中的调试器。
1. 打开名为 `Controllers/HomeController.cs` 的代码文件，编辑“关于”页面将显示的消息：`ViewData["Message"] = "Your application description page.";`
1. 保存文件。
1. 按 **F5** 再次开始调试。 

Azure Dev Spaces 不会在每次进行代码编辑时都重新生成和重新部署新的容器映像（这通常需要很长时间），而是在现有的容器中以增量方式重新编译代码，加快编辑/调试循环速度。

刷新浏览器中的 Web 应用，然后转到“关于”页面。 此时会看到自定义消息显示在 UI 中。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用多个容器和团队开发](get-started-netcore-visualstudio.md#call-another-container)