---
title: 使用 VS Code 在云中创建 Kubernetes Node.js 开发环境 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 09/26/2018
ms.topic: tutorial
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
ms.openlocfilehash: 6cdce9f7dbb2e3f0bc5b2d8a3f12e5d50cadc505
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816957"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>通过 Node.js 开始使用 Azure Dev Spaces

本指南介绍如何：

- 在 Azure 中创建针对开发进行了优化的基于 Kubernetes 的环境 - _开发空间_。
- 在容器中使用 VS Code 和命令行以迭代方式开发代码。
- 在团队环境中高效地开发和测试代码。

> [!Note]
> **如果在任何时候遇到问题**，请参阅[故障排除](troubleshooting.md)部分，或在此页上发表评论。

现在可以在 Azure 中创建基于 Kubernetes 的开发环境了。

## <a name="install-the-azure-cli"></a>安装 Azure CLI
Azure Dev Spaces 需要进行最基本的本地计算机设置。 开发空间的大部分配置存储在云中，可以与其他用户共享。 本地计算机可以运行 Windows、Mac 或 Linux。 对于 Linux，支持以下发行版：Ubuntu（18.04、16.04 和 14.04），Debian 8 和 9、RHEL 7、Fedora 26+、CentOS 7、openSUSE 42.2 和 SLES 12。

首先，请下载并运行 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 

> [!IMPORTANT]
> 如果已安装 Azure CLI，请确保使用 2.0.43 版或更高版本。

### <a name="sign-in-to-azure-cli"></a>登录 Azure CLI
登录 Azure。 在终端窗口中键入以下命令：

```cmd
az login
```

> [!Note]
> 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。

#### <a name="if-you-have-multiple-azure-subscriptions"></a>如果有多个 Azure 订阅...
通过运行以下命令可以查看订阅： 

```cmd
az account list
```
在 JSON 输出中找到具有 `isDefault: true` 的订阅。
如果这不是你想要使用的订阅，可以更改默认订阅：

```cmd
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>创建为 Azure Dev Spaces 启用的 Kubernetes 群集

在命令提示符下，创建资源组。 使用当前支持的区域（EastUS、EastUS2、CentralUS、WestUS2、WestEurope、SoutheastAsia、CanadaCentral 或 CanadaEast）之一。

```cmd
az group create --name MyResourceGroup --location <region>
```

使用以下命令创建 Kubernetes 群集：

```cmd
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.10.9 --generate-ssh-keys
```

创建群集需要几分钟时间。

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>配置 AKS 群集以使用 Azure Dev Spaces

使用包含 AKS 群集的资源组和 AKS 群集名称输入以下 Azure CLI 命令。 此命令将群集配置为支持 Azure Dev Spaces。

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

> [!IMPORTANT]
> Azure Dev Spaces 配置过程会删除群集中的 `azds` 命名空间（如果存在）。

## <a name="get-kubernetes-debugging-for-vs-code"></a>获取 VS Code 的 Kubernetes 调试功能
丰富的功能（如 Kubernetes调试）可供使用 VS Code 的 .NET Core 和 Node.js 开发人员使用。

1. 如果尚未安装 [VS Code](https://code.visualstudio.com/Download)，请安装它。
1. 下载并安装 [VS Azure Dev Spaces 扩展](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)。 在扩展的“市场”页上单击“安装”一次，然后再在 VS Code 中单击一次。 

## <a name="create-a-nodejs-container-in-kubernetes"></a>在 Kubernetes 中创建 Node.js 容器

在此部分，需创建一个 Node.js Web 应用并让其在 Kubernetes 的容器中运行。

### <a name="create-a-nodejs-web-app"></a>创建 Node.js Web 应用
请从 GitHub 下载代码，方法是：导航到 https://github.com/Azure/dev-spaces，然后选择“Clone or Download”（克隆或下载），将 GitHub 存储库下载到本地环境。 本指南的代码位于 `samples/nodejs/getting-started/webfrontend` 中。

## <a name="prepare-code-for-docker-and-kubernetes-development"></a>准备用于 Docker 和 Kubernetes 开发的代码
到目前为止，已有一个可以在本地运行的基本 Web 应用。 现在，将通过创建定义应用的容器以及将应用部署到 Kubernetes 的方式的资产来将其容器化。 使用 Azure Dev Spaces，可以很容易完成此任务： 

1. 启动 VS Code 并打开 `webfrontend` 文件夹。 （可以忽略任何默认提示以添加调试资产或还原项目。）
1. 在 VS Code 中打开集成终端（使用“视图”>“集成终端”菜单）。
1. 运行此命令（确保 **webfrontend** 是当前文件夹）：

    ```cmd
    azds prep --public
    ```

Azure CLI 的 `azds prep` 命令使用默认设置生成 Docker 和 Kubernetes 资产：
* `./Dockerfile` 描述应用的容器映像，以及如何在容器内生成和运行源代码。
* `./charts/webfrontend` 下面的 [Helm 图表](https://docs.helm.sh)描述如何将容器部署到 Kubernetes。

暂无必要了解这些文件的全部内容。 但是，值得指出的是，**相同的 Kubernetes 和 Docker 配置即代码资产可以从开发一直用到生产，从而在不同的环境中提供更好的一致性。**
 
名为 `./azds.yaml` 的文件也由 `prep` 命令生成，并且它是 Azure Dev Spaces 的配置文件。 它通过额外配置对 Docker 和 Kubernetes 项目进行了补充，从而在 Azure 中实现了迭代开发体验。

## <a name="build-and-run-code-in-kubernetes"></a>在 Kubernetes 中生成并运行代码
让我们运行代码！ 在终端窗口中，从**根代码文件夹** webfrontend 运行以下命令：

```cmd
azds up
```

密切关注命令的输出，随着进展，你会注意到几件事情：
- 源代码已同步到 Azure 中的开发空间。
- 容器映像将在 Azure 中生成，由代码文件夹中的 Docker 资产指定。
- 将利用代码文件夹中 Helm 图表指定的容器映像创建 Kubernetes 对象。
- 将显示有关容器终结点的信息。 在我们的示例中，我们需要公共 HTTP URL。
- 假设上述阶段成功完成，你应该在容器启动时开始看到 `stdout`（和 `stderr`）输出。

> [!Note]
> 第一次运行 `up` 命令时，这些步骤需要较长时间，但后续运行应该会更快。

### <a name="test-the-web-app"></a>测试 Web 应用
扫描控制台输出以获取有关由 `up` 命令创建的公共 URL 的信息。 它的形式如下： 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 80 (TCP) is available at http://localhost:<port>
```

在浏览器窗口中打开此 URL，你应看到 Web 应用加载。 在容器执行时，`stdout` 和 `stderr` 输出将流式传输到终端窗口。

> [!Note]
> 首次运行时，公共 DNS 可能要花费几分钟时间才能准备就绪。 如果公共 URL 无法解析，可以使用控制台输出中显示的替代 http://localhost:<portnumber> URL。 如果使用该 localhost URL，则容器看起来是在本地运行，但实际上是在 AKS 中运行。 为方便操作以及便于与本地计算机中的服务交互，Azure Dev Spaces 将与 Azure 中运行的容器建立临时的 SSH 隧道。 你可以返回，稍后在 DNS 记录准备就绪时再尝试公共 URL。

### <a name="update-a-content-file"></a>更新内容文件
Azure Dev Spaces 不仅仅是用来让代码在 Kubernetes 中运行，它还可以用来快速地以迭代方式查看所做的代码更改在云的 Kubernetes 环境中的效果。

1. 找到 `./public/index.html` 文件，对 HTML 进行编辑。 例如，将页面的背景色更改为蓝色：

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. 保存文件。 稍后会在终端窗口中看到一条消息，指出正在运行的容器中的文件已更新。
1. 转到浏览器并刷新页面。 此时会看到颜色更新。

发生了什么情况？ 对内容文件（例如 HTML 和 CSS）所做的编辑不需要 Node.js 进程重启，因此活动的 `azds up` 命令会自动将任何修改的内容文件直接同步到 Azure 中正在运行的容器，方便你快速查看所做的内容编辑。

### <a name="test-from-a-mobile-device"></a>从移动设备进行测试
使用 webfrontend 的公共 URL 在移动设备上打开该 Web 应用。 你可能希望将该 URL 从桌面复制并发送到设备，以避免输入长长的地址。 当该 Web 应用在移动设备中加载时，你会注意到 UI 在小型设备上不能正确显示。

若要修复此问题，可添加一个 `viewport` meta 标记：
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

此命令重新生成容器映像并重新部署 Helm 图表。 重新加载浏览器页面即可查看代码更改的效果。

不过，还有一种更快的开发代码的方法，该方法在下一部分介绍。 

## <a name="debug-a-container-in-kubernetes"></a>在 Kubernetes 中调试容器

在本部分中，将使用 VS Code 直接调试在 Azure 中运行的容器。 你还将学习如何实现更快的“编辑-运行-测试”循环。

![](media/common/edit-refresh-see.png)

> [!Note]
> **如果在任何时候遇到问题**，请参阅[故障排除](troubleshooting.md)部分，或在此页上发表评论。

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>使用 VS Code 扩展初始化调试资产
首先需要配置代码项目，以便 VS Code 与 Azure 中的开发空间进行通信。 Azure Dev Spaces 的 VS Code 扩展提供了一个帮助程序命令来设置调试配置。 

打开**命令面板**（使用“视图”|“命令面板”菜单），并使用“自动完成”来键入并选择此命令：`Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。 

这将在 `.vscode` 文件夹下为 Azure Dev Spaces 添加调试配置。 此命令不应与 `azds prep` 命令混淆，后者配置部署的项目。

![](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>选择 AZDS 调试配置
1. 若要打开“调试”视图，请单击 VS Code 侧**活动栏**中的“调试”图标。
1. 选择“启动程序(AZDS)”作为活动的调试配置。

![](media/get-started-node/debug-configuration-nodejs2.png)

> [!Note]
> 如果在命令面板中看不到任何 Azure Dev Spaces 命令，请确保已[安装 Azure Dev Spaces 的 VS Code 扩展](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code)。

### <a name="debug-the-container-in-kubernetes"></a>在 Kubernetes 中调试容器
按 **F5** 在 Kubernetes 中调试代码！

与 `up` 命令类似，代码会在开始调试后同步到开发环境，而容器则会在生成后部署到 Kubernetes。 这次调试器会附加到远程容器。

> [!Tip]
> VS Code 状态栏会显示一个可点击的 URL。

![](media/common/vscode-status-bar-url.png)

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
*Nodemon* 是一种常用的工具，可供 Node.js 开发人员用来进行快速开发。 开发人员通常不会每次进行服务器端代码编辑都手动重启 Node 进程，而是将其 Node 项目配置为通过 *nodemon* 监视文件更改并自动重启服务器进程。 使用这种工作方式，开发人员只需在进行代码编辑后刷新其浏览器即可。

使用 Azure Dev Spaces 时，可以使用的许多开发工作流与在本地开发无异。 为了说明这一点，示例性的 `webfrontend` 项目配已置为使用 *nodemon*（在 `package.json` 中配置为开发依赖项）。

请尝试以下步骤：
1. 停止 VS Code 调试器。
1. 单击 VS Code 侧**活动栏**中的“调试”图标。 
1. 选择“附加(AZDS)”作为活动的调试配置。
1. 按 F5。

在此配置中，容器配置为启动 *nodemon*。 进行服务器代码编辑时，*nodemon* 会自动重启 Node 进程，就像在本地开发一样。 
1. 在 `server.js` 中再次编辑 hello 消息，然后保存文件。
1. 刷新浏览器或单击“再说一遍”按钮即可看到更改生效！

**现在，我们已掌握了一种快速迭代代码并直接在 Kubernetes 中进行调试的方法！** 接下来，我们了解如何创建和调用另一个容器。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解多服务开发](multi-service-nodejs.md)

