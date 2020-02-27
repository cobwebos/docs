---
title: 快速入门：创建 Node.js Web 应用
description: 在数分钟内将第一个 Node.js Hello World 部署到 Azure 应用服务。 我们使用 Visual Studio Code 进行部署，这是部署到应用服务时使用的多种方法中的一种。
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 09/30/2019
ms.custom: seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 3fcc4d9d33637cd61f5621cc95788e9de8ffaec6
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500115"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>在 Azure 中创建 Node.js Web 应用 

Azure 应用服务提供高度可缩放、自修补的 Web 托管服务。 本快速入门演示如何将 Node.js 应用部署到 Azure 应用服务中。

## <a name="prerequisites"></a>必备条件

如果你没有 Azure 帐户，请[立即注册](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)一个免费帐户来试用任何服务组合，并获得 200 美元的 Azure 信用额度。

需要连同 [Node.js 和 npm](https://nodejs.org/en/download)（Node.js 包管理器）一起安装 [Visual Studio Code](https://code.visualstudio.com/)。

还需要安装 [Azure 应用服务扩展](vscode:extension/ms-azuretools.vscode-azureappservice)，使用该扩展可在 Azure 平台即服务 (PaaS) 上创建、管理和部署 Linux Web 应用。

### <a name="sign-in"></a>登录

安装该扩展后，登录到你的 Azure 帐户。 在活动栏中，选择 Azure 徽标显示“AZURE 应用服务”资源管理器。  选择“登录到 Azure...”并遵照说明操作。 

![登录到 Azure](containers/media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>故障排除

如果出现错误“找不到名为 [订阅 ID] 的订阅”，原因可能是你使用了代理，因此无法访问 Azure API。  在终端中使用 `export` 以代理信息配置 `HTTP_PROXY` 和 `HTTPS_PROXY` 环境变量。

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

如果设置环境变量不能解决该问题，请选择下面的“我遇到了问题”按钮联系我们。 

### <a name="prerequisite-check"></a>先决条件检查

在继续之前，请确保已安装并配置了所有必备组件。

在 VS Code 的状态栏中应会显示你的 Azure 电子邮件地址，“AZURE 应用服务”资源管理器中应会显示你的订阅。 

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>创建 Node.js 应用程序

接下来，创建可部署到云的 Node.js 应用程序。 本快速入门使用一个应用程序生成器从终端快速搭建应用程序。

> [!TIP]
> 如果已完成 [Node.js 教程](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)，可直接跳转到[部署到 Azure](#deploy-to-azure)。

### <a name="scaffold-a-new-application-with-the-express-generator"></a>使用快速生成器搭建新应用程序

[Express](https://www.expressjs.com) 是用于生成和运行 Node.js 应用程序的流行框架。 可以使用 [Express 生成器](https://expressjs.com/en/starter/generator.html)工具搭建（创建）新的 Express 应用程序。 快速生成器作为 npm 模块提供，可以使用 npm 命令行工具 `npx` 直接运行（无需安装）。

```bash
npx express-generator myExpressApp --view pug --git
```

`--view pug --git` 参数告知生成器使用 [pug](https://pugjs.org/api/getting-started.html) 模板引擎（以前称为 `jade`）并创建 `.gitignore` 文件。

若要安装应用程序的所有依赖项，请转到新文件夹并运行 `npm install`。

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>运行应用程序

接下来，确保运行应用程序。 在终端中，使用 `npm start` 命令启动该应用程序以启动服务器。

```bash
npm start
```

现在，打开浏览器并导航到 [http://localhost:3000](http://localhost:3000)，其中应会显示如下所示的内容：

![运行 Express 应用程序](containers/media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>“部署到 Azure”

在本部分中，将使用 VS Code 和 Azure 应用服务扩展部署 Node.js 应用。 本快速入门使用最基本的部署模型，其中，应用程序将被压缩并部署到 Linux 上的 Azure Web 应用。

### <a name="deploy-using-azure-app-service"></a>使用 Azure 应用服务进行部署

首先，在 VS Code 中打开应用程序文件夹。

```bash
code .
```

在“AZURE 应用服务”资源管理器中，选择蓝色的向上箭头图标，将应用部署到 Azure。 

![部署到 Web 应用](containers/media/quickstart-nodejs/deploy.png)

> [!TIP]
> 也可以从**命令面板** (CTRL + SHIFT + P) 进行部署，方法是键入“deploy to web app”并运行“Azure App Service:  Deploy to Web App”命令。

1. 选择当前已打开的目录 `myExpressApp`。

1. 根据要部署到的操作系统选择创建选项：

    - Linux：选择“创建新 Web 应用”  。
    - Windows：选择“创建新 Web 应用”，然后选择“高级”选项  。 

1. 键入 Web 应用的全局唯一名称，然后按 ENTER。 应用名称的有效字符为“a-z”、“0-9”和“-”。

1. 如果以 Linux 为目标，请在出现提示时选择 Node.js 版本。 建议使用 **LTS** 版本。

1. 如果使用“高级”  选项定向 Windows，请按照其他提示操作：
    1. 选择“创建新的资源组”  ，然后输入资源组的名称。
    1. 选择 **Windows** 作为操作系统。
    1. 选择现有的应用服务计划或创建新的计划。 可以在创建新计划时选择定价层。
    1. 当系统提示 Application Insights 时，选择“立即跳过”  。
    1. 选择你附近或想要访问的资源附近的区域。

1. 响应所有提示后，通知通道将显示为应用创建的 Azure 资源。

1. 当系统提示你更新配置以在目标服务器上运行 `npm install` 时，选择“是”。  随后将部署应用。

    ![配置的部署](containers/media/quickstart-nodejs/server-build.png)

1. 部署开始后，系统会提示更新工作区，使以后的部署自动针对相同的应用服务 Web 应用。 选择“是”，以确保将更改部署到正确的应用。 

    ![配置的部署](containers/media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> 确保应用程序正在侦听 PORT 环境变量 `process.env.PORT` 提供的端口。

### <a name="browse-the-app-in-azure"></a>在 Azure 中浏览应用

部署完成后，选择提示中的“浏览网站”以查看全新部署的 Web 应用。 

### <a name="troubleshooting"></a>故障排除

如果看到错误“你无权查看此目录或页面”，则表示应用程序可能无法正常启动。  请转到下一部分，查看日志输出以找到并修复错误。 如果无法解决此问题，请选择下面的“我遇到了问题”按钮联系我们。  我们很乐意为你提供帮助。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>更新应用

可以使用相同的过程来部署对此应用所做的更改，并选择现有应用而不是创建新应用。

## <a name="viewing-logs"></a>查看日志

本部分介绍如何从运行的应用服务应用查看（或“跟踪”）日志。 对应用中的 `console.log` 发出的任何调用将显示在 Visual Studio Code 的输出窗口中。

在“AZURE 应用服务”资源管理器中找到该应用，右键单击该应用，然后选择“查看流日志”。  

出现提示时，请选择启用日志记录并重启应用程序。 重启应用后，VS Code 输出窗口将会打开，其中包含与日志流建立的连接。

![查看流日志](containers/media/quickstart-nodejs/view-logs.png)

![启用日志记录并重启](containers/media/quickstart-nodejs/enable-restart.png)

几秒钟后，你将看到一条消息，指出已连接到日志流服务。 请多次刷新页面以查看更多活动。

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    ```

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>后续步骤

祝贺你，你现已成功完成本快速入门！

接下来请查看其他 Azure 扩展。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker 工具](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI 工具](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

或安装 [Node Pack for Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 扩展包获取所有这些工具。
