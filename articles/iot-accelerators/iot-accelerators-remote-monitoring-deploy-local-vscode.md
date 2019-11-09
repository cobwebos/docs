---
title: 在本地部署远程监视解决方案-Visual Studio Code-Azure |Microsoft Docs
description: 本操作指南介绍了如何使用 Visual Studio Code 将远程监视解决方案加速器部署到本地计算机，以用于测试和开发。
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890959"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>在本地部署远程监视解决方案加速器 - Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

本文展示了如何将远程监视解决方案加速器部署到本地计算机，用于测试和开发。 了解如何在 Visual Studio Code 中运行微服务。 本地微服务部署使用以下云服务： IoT 中心、Cosmos DB、Azure 流分析和 Azure 时序见解。

## <a name="prerequisites"></a>必备组件

若要部署远程监视解决方案加速器使用的 Azure 服务，需要一个有效的 Azure 订阅。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="machine-setup"></a>计算机设置

若要完成本地部署，需要使用在本地开发计算机上安装的以下工具：

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code 的 C# 扩展](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) - 此软件是 PCS CLI 的先决条件，脚本使用 PCS CLI 来创建 Azure 资源。 请勿使用 Node.js v10

> [!NOTE]
> Visual Studio Code 适用于 Windows、Mac 和 Ubuntu。

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>运行微服务

在本部分，我们将运行远程监视微服务。 在本机运行 Web UI，在 Docker 中运行设备模拟服务，并在 Visual Studio Code 中运行微服务。

### <a name="build-the-code"></a>生成代码

在命令提示符下导航到 azure-iot-pcs-remote-monitoring-dotnet\services，然后运行以下命令生成代码。

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>在本地计算机上部署其他所有微服务

以下步骤说明如何在 Visual Studio Code 中运行远程监视微服务：

1. 启动 Visual Studio Code。
1. 在 VS Code 中，打开 " **dotnet** " 文件夹。
1. 在 " **dotnet** " 文件夹中创建名为**vscode**的新文件夹。
1. 将**services\scripts\local\launch\idesettings\vscode 和**vscode**文件中的**文件复制到刚创建的文件夹。
1. 在 VS Code 中打开 "**调试" 面板**，并运行 "**运行所有微服务**" 配置。 此配置会在 Docker 中运行设备模拟微服务，而在调试器中运行其他微服务。

运行的输出运行调试控制台中的**所有 microsoervices**如下所示：

[![微服务](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>运行 Web UI

此步骤启动 Web UI。 导航到本地副本中的 **azure-iot-pcs-remote-monitoring-dotnet\webui** 文件夹，然后运行以下命令：

```cmd
npm install
npm start
```

启动完成后，浏览器将显示**http：\//localhost： 3000/仪表板**的页面。 此页面上出现的错误在意料之中。 若要在无错误的情况下查看应用程序，请完成以下步骤。

### <a name="configure-and-run-nginx"></a>配置并运行 NGINX

设置反向代理服务器，以链接本地计算机上运行的 Web 应用程序和微服务：

* 将 **webui\scripts\localhost** 文件夹中的 **nginx.conf** 文件复制到 **nginx\conf** 安装目录。
* 运行 **nginx**。

有关运行 **nginx** 的详细信息，请参阅[适用于 Windows 的 nginx](https://nginx.org/en/docs/windows.html)。

### <a name="connect-to-the-dashboard"></a>连接到仪表板

若要访问远程监视解决方案仪表板，请在浏览器中导航到 http：\//localhost：9000。

## <a name="clean-up"></a>清理

为避免产生不必要的费用，在完成测试后，请从 Azure 订阅中删除云服务。 若要删除这些服务，请导航到 [Azure 门户](https://ms.portal.azure.com)，并删除 **start.cmd** 脚本创建的资源组。

还可以删除在从 GitHub 克隆源代码时创建的远程监视存储库的本地副本。

## <a name="next-steps"></a>后续步骤

部署远程监视解决方案后，接下来请[探索解决方案仪表板的功能](quickstart-remote-monitoring-deploy.md)。
