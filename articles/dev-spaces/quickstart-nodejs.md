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
ms.openlocfilehash: 764606d838ac067a09072b84222a8ec092c4c124
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823201"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-nodejs"></a>快速入门：使用 Azure Dev Spaces 创建 Kubernetes 开发空间 (Node.js)

本指南介绍如何：

- 使用 Azure 中托管的 Kubernetes 群集设置 Azure Dev Spaces。
- 在容器中使用 VS Code 和命令行以迭代方式开发代码。
- 调试群集中运行的代码。

> [!Note]
> **如果你在任何时候遇到问题**，请参见[故障排除](troubleshooting.md)部分，或在此页上发表评论。 也可以试着学习更详细的[教程](get-started-nodejs.md)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
- 在 EastUS、WestEurope 或 CanadaEast 区域中运行 Kubernetes 1.9.6 且已启用“HTTP 应用程序路由”的 [Kubernetes 群集](https://ms.portal.azure.com/#create/microsoft.aks)。

  ![请务必启用“Http 应用程序路由”。](media/common/Kubernetes-Create-Cluster-3.PNG)

- Visual Studio Code，可在[此处](https://code.visualstudio.com/download)下载。

## <a name="set-up-azure-dev-spaces"></a>设置 Azure Dev Spaces

1. 安装 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)（版本 2.0.33 或更高版本）。
1. 在 AKS 群集上设置 Dev Spaces：`az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. 下载适用于 VS Code 的 [Azure Dev Spaces 扩展](https://aka.ms/get-azds-code)。
1. 安装扩展：`code --install-extension path-to-downloaded-extension/azds-0.1.1.vsix`

## <a name="build-and-run-code-in-kubernetes"></a>在 Kubernetes 中生成并运行代码

1. 从 GitHub 下载示例代码：[https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. 将目录切换到 webfrontend 文件夹：`cd dev-spaces/samples/nodejs/getting-started/webfrontend`
1. 生成 Docker 和 Helm 图表资产：`azds prep --public`
1. 在 AKS 中构建开发空间。 在终端窗口中，从**根代码文件夹** webfrontend 运行以下命令：`azds up`
1. 扫描控制台输出以获取有关由 `up` 命令创建的 URL 的信息。 它的形式如下： 

   `Service 'webfrontend' port 'http' is available at <url>` 

   在浏览器窗口中打开此 URL，你应看到 Web 应用加载。 

### <a name="update-a-content-file"></a>更新内容文件
Azure Dev Spaces 不仅仅是用来让代码在 Kubernetes 中运行，它还可以用来快速地以迭代方式查看所做的代码更改在云的 Kubernetes 环境中的效果。

1. 找到 `./public/index.html` 文件，对 HTML 进行编辑。 例如，将页面的背景色更改为蓝色：

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

1. 保存文件。 稍后会在终端窗口中看到一条消息，指出正在运行的容器中的文件已更新。
1. 转到浏览器并刷新页面。 此时会看到颜色更新。

发生了什么情况？ 对内容文件（例如 HTML 和 CSS）所做的编辑不需要 Node.js 进程重启，因此活动的 `azds up` 命令会自动将任何修改的内容文件直接同步到 Azure 中正在运行的容器，方便你快速查看所做的内容编辑。

### <a name="test-from-a-mobile-device"></a>从移动设备进行测试
如果在移动设备上打开 Web 应用，你会注意到 UI 在小型设备上显示不正确。

若要修复此问题，可添加一个 `viewport` META 标记：
1. 打开 `./public/index.html` 文件
1. 在现有的 `head` 元素中添加 `viewport` META 标记：

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. 保存文件。
1. 刷新设备的浏览器。 此时会看到 Web 应用正确地呈现。 

此示例说明了某些问题只有在使用相应应用的设备上进行测试才会被发现。 使用 Azure Dev Spaces 可以快速迭代代码，并在目标设备上验证任何更改。

### <a name="update-a-code-file"></a>更新代码文件
更新服务器端代码文件需要的工作多一些，因为 Node.js 应用需重启。

1. 在终端窗口中按 `Ctrl+C`（用于停止 `azds up`）。
1. 打开名为 `server.js` 的代码文件，编辑服务的 hello 消息： 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. 保存文件。
1. 在终端窗口中运行 `azds up`。 

这样会重新生成容器映像并重新部署 Helm 图表。 重新加载浏览器页面即可查看代码更改的效果。

不过，还有一种更快的开发代码的方法，该方法在下一部分介绍。 

## <a name="debug-a-container-in-kubernetes"></a>在 Kubernetes 中调试容器

在本部分中，将使用 VS Code 直接调试在 Azure 中运行的容器。 你还将学习如何实现更快的“编辑-运行-测试”循环。

![](./media/common/edit-refresh-see.png)

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>使用 VS Code 扩展初始化调试资产
首先需要配置代码项目，以便 VS Code 与 Azure 中的开发空间进行通信。 Azure Dev Spaces 的 VS Code 扩展提供了一个帮助程序命令来设置调试配置。 

打开**命令面板**（使用“视图”|“命令面板”菜单），并使用“自动完成”来键入并选择此命令：`Azure Dev Spaces: Create configuration files for connected development`。 

这将在 `.vscode` 文件夹下为 Azure Dev Spaces 添加调试配置。

![](./media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>选择 AZDS 调试配置
1. 若要打开“调试”视图，请单击 VS Code 侧**活动栏**中的“调试”图标。
1. 选择“启动程序(AZDS)”作为活动的调试配置。

![](media/get-started-node/debug-configuration-nodejs.png)

> [!Note]
> 如果在命令面板中看不到任何 Azure Dev Spaces 命令，请确保已安装 Azure Dev Spaces 的 VS Code 扩展。

### <a name="debug-the-container-in-kubernetes"></a>在 Kubernetes 中调试容器
按 **F5** 在 Kubernetes 中调试代码！

与 `up` 命令类似，代码会在开始调试后同步到开发空间，而容器则会在生成后部署到 Kubernetes。 这次调试器会附加到远程容器。

> [!Tip]
> VS Code 状态栏会显示一个可点击的 URL。

在服务器端的代码文件中设置一个断点，例如，在 `server.js` 的 `app.get('/api'...` 中设置断点。 刷新浏览器页面，或者按“再说一遍”按钮，然后即可按断点对代码进行单步调试。

可以不受限制地访问调试信息（例如调用堆栈、本地变量、异常信息等），就像在本地执行代码一样。

### <a name="edit-code-and-refresh-the-debug-session"></a>编辑代码并刷新调试会话
在调试器处于活动状态的情况下进行代码编辑，例如再次修改 hello 消息：

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

保存文件，然后在“调试操作”窗格中单击“刷新”按钮。 

![](media/get-started-node/debug-action-refresh-nodejs.png)

Azure Dev Spaces 不会在每次进行代码编辑时都重新生成和重新部署新的容器映像（这通常需要很长时间），而是在两次调试会话期间重启 Node.js 进程，加快编辑/调试循环速度。

刷新浏览器中的 Web 应用，或者按“再说一遍”按钮。 此时会看到自定义消息显示在 UI 中。

### <a name="use-nodemon-to-develop-even-faster"></a>使用 NodeMon 加快开发速度

示例 `webfrontend` 项目已配置为使用 [nodemon](https://nodemon.io/)，这是一种流行的工具，用于加速与 Azure Dev Spaces 完全兼容的 Node.js 开发。

请尝试以下步骤：
1. 停止 VS Code 调试器。
1. 单击 VS Code 侧**活动栏**中的“调试”图标。 
1. 选择“附加(AZDS)”作为活动的调试配置。
1. 按 F5。

在此配置中，容器配置为启动 *nodemon*。 进行服务器代码编辑时，*nodemon* 会自动重启 Node 进程，就像在本地开发一样。 
1. 在 `server.js` 中再次编辑 hello 消息，然后保存文件。
1. 刷新浏览器或单击“再说一遍”按钮即可看到更改生效！

**现在，我们已掌握了一种快速迭代代码并直接在 Kubernetes 中进行调试的方法！**

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用多个容器和团队开发](get-started-nodejs.md#call-a-service-running-in-a-separate-container)