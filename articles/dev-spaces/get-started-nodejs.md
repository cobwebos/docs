---
title: 使用 VS Code 在云中创建 Kubernetes Node.js 开发环境 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
manager: douge
ms.openlocfilehash: efd19393a661a48a566e85a058dad071c3bdb63c
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945983"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>通过 Node.js 开始使用 Azure Dev Spaces

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

现在可以在 Azure 中创建基于 Kubernetes 的开发环境了。

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>安装 Azure CLI
Azure Dev Spaces 需要进行最基本的本地计算机设置。 开发环境的大部分配置存储在云中，可以与其他用户共享。 首先，请下载并运行 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

> [!IMPORTANT]
> 如果已安装 Azure CLI，请确保使用 2.0.38 版或更高版本。

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

可以一边等待群集的创建，一边开始编写代码。

## <a name="create-a-nodejs-container-in-kubernetes"></a>在 Kubernetes 中创建 Node.js 容器

在此部分，需创建一个 Node.js Web 应用并让其在 Kubernetes 的容器中运行。

### <a name="create-a-nodejs-web-app"></a>创建 Node.js Web 应用
请从 GitHub 下载代码，方法是：导航到 https://github.com/Azure/dev-spaces，然后选择“Clone or Download”（克隆或下载），将 GitHub 存储库下载到本地环境。 本指南的代码位于 `samples/nodejs/getting-started/webfrontend` 中。

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

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

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>选择 AZDS 调试配置
1. 若要打开“调试”视图，请单击 VS Code 侧**活动栏**中的“调试”图标。
1. 选择“启动程序(AZDS)”作为活动的调试配置。

![](media/get-started-node/debug-configuration-nodejs2.png)

> [!Note]
> 如果在命令面板中看不到任何 Azure Dev Spaces 命令，请确保已[安装 Azure Dev Spaces 的 VS Code 扩展](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code)。

### <a name="debug-the-container-in-kubernetes"></a>在 Kubernetes 中调试容器
按 **F5** 在 Kubernetes 中调试代码！

与 `up` 命令类似，代码会在开始调试后同步到开发环境，而容器则会在生成后部署到 Kubernetes。 这次调试器会附加到远程容器。

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

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

使用 Azure Dev Spaces 时，可以使用的许多开发工作流与在本地开发无异。 示例性的 `webfrontend` 项目配置为使用 *nodemon*（在 `package.json` 中配置为开发依赖项）就是为了说明这一点。

请尝试以下步骤：
1. 停止 VS Code 调试器。
1. 单击 VS Code 侧**活动栏**中的“调试”图标。 
1. 选择“附加(AZDS)”作为活动的调试配置。
1. 按 F5。

在此配置中，容器配置为启动 *nodemon*。 进行服务器代码编辑时，*nodemon* 会自动重启 Node 进程，就像在本地开发一样。 
1. 在 `server.js` 中再次编辑 hello 消息，然后保存文件。
1. 刷新浏览器或单击“再说一遍”按钮即可看到更改生效！

**现在，我们已掌握了一种快速迭代代码并直接在 Kubernetes 中进行调试的方法！** 接下来，我们了解如何创建和调用另一个容器。

## <a name="call-a-service-running-in-a-separate-container"></a>调用独立容器中运行的服务

在本部分，我们将创建另一个服务 `mywebapi`，并让 `webfrontend` 调用它。 每个服务将在独立的容器中运行。 然后，我们将跨两个容器进行调试。

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>打开 *mywebapi* 的示例代码
名为 `samples` 的文件夹下应已包含本指南中 `mywebapi` 的示例代码（如果没有，请转到 https://github.com/Azure/dev-spaces，并选择“克隆或下载”以下载 GitHub 存储库。）本部分的代码位于 `samples/nodejs/getting-started/mywebapi` 中。

### <a name="run-mywebapi"></a>运行 *mywebapi*
1. 在单独的 VS Code 窗口中打开文件夹 `mywebapi`。
1. 按 F5，等待服务生成和部署。 当 VS Code 调试栏出现时，表示服务已准备就绪。
1. 记下终结点 URL（类似于 http://localhost:\<portnumber\>）。 **提示：VS Code 状态栏会显示一个可点击的 URL。** 容器看起来是在本地运行，但实际上是在 Azure 上的开发环境中运行。 出现 localhost 地址的原因是，`mywebapi` 尚未定义任何公共终结点，只能从 Kubernetes 实例内部访问。 为方便操作以及便于与本地计算机中的专用服务交互，Azure Dev Spaces 将与 Azure 中运行的容器建立临时的 SSH 隧道。
1. 当 `mywebapi` 准备就绪时，请在浏览器中打开 localhost 地址。 应会看到 `mywebapi` 服务发来的响应（“Hello from mywebapi”）。


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>从 *webfrontend* 向 *mywebapi* 发出请求
现在，让我们在 `webfrontend` 中编写用于向 `mywebapi` 发出请求的代码。
1. 切换到 `webfrontend` 的 VS Code 窗口。
1. 在 `server.js` 的顶部添加以下代码行：
    ```javascript
    var request = require('request');
    ```

3. 替换 `/api` GET 处理程序的代码。 处理请求时，该代码又会调用 `mywebapi`，然后返回来自这两个服务的结果。

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```

上面的代码示例将传入请求中的 `azds-route-as` 标头转发给传出请求。 本文稍后将会介绍此函数如何帮助团队实现协作性开发。

### <a name="debug-across-multiple-services"></a>跨多个服务进行调试
1. 此时，`mywebapi` 应该仍是在附加了调试程序的情况下运行。 如果不是，请在 `mywebapi` 项目中按 F5。
1. 在默认的 GET `/` 处理程序中设置断点。
1. 在 `webfrontend` 项目中，紧靠在向 `http://mywebapi` 发送 GET 请求的代码位置前面设置一个断点。
1. 在 `webfrontend` 项目中按 F5。
1. 打开 Web 应用，并对两个服务中的代码进行单步调试。 Web 应用应会显示一条由两个服务串联的消息：“Hello from webfrontend and Hello from mywebapi”。

干得不错！ 现已创建一个多容器应用程序，其中的每个容器可单独进行开发或部署。

## <a name="learn-about-team-development"></a>了解团队开发

[!INCLUDE[](includes/team-development-1.md)]

现在，让我们看看该项目的运行情况：
1. 转到 `mywebapi` 的 VS Code 窗口，并对默认的 GET `/` 处理程序进行代码编辑，例如：

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]




