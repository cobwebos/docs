---
title: 快速入门：创建 Node.js Web 应用
description: 在数分钟内将第一个 Node.js Hello World 部署到 Azure 应用服务。 我们使用 Visual Studio Code 进行部署，这是部署到应用服务时使用的多种方法中的一种。
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: df1e0809c0122eae593543cd193a6477b03d0938
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690797"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>在 Azure 中创建 Node.js Web 应用 

通过使用 Visual Studio Code 在本地创建 Node.js/Express 应用并将其部署到云，来开始使用 Azure 应用服务。 由于使用的是免费应用服务层，因此完成本快速入门不会产生费用。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)。
- [Node.js 和 npm](https://nodejs.org)。 运行命令 `node --version` 验证是否已安装 Node.js。
- [Visual Studio Code](https://code.visualstudio.com/)。
- Visual Studio Code 的 [Azure 应用服务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。

## <a name="clone-and-run-a-local-nodejs-application"></a>克隆并运行本地 Node.js 应用程序

1. 在本地计算机上，打开终端并克隆示例存储库：

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. 导航到新应用所在的文件夹：

    ```bash
    cd nodejs-docs-hello-world
    ```

1. 启动该应用以在本地进行测试：

    ```bash
    npm start
    ```
    
1. 打开浏览器并导航到 `http://localhost:1337`。 浏览器应会显示“Hello World!”。

1. 在终端中按 **Ctrl**+**C** 停止服务器。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>将应用部署到 Azure

在本部分，你将使用 VS Code 和 Azure 应用服务扩展将 Node.js 应用部署到 Azure。

1. 在终端中，确保你在 *nodejs-docs-hello-world* 文件夹中操作，然后使用以下命令启动 Visual Studio Code：

    ```bash
    code .
    ```

1. 在 VS Code 活动栏中，选择 Azure 徽标显示“AZURE 应用服务”资源管理器。  选择“登录到 Azure...”并遵照说明操作。  （如果遇到错误，请参阅下面的[排查 Azure 登录问题](#troubleshooting-azure-sign-in)。）登录后，资源管理器应会显示 Azure 订阅的名称。

    ![登录 Azure](containers/media/quickstart-nodejs/sign-in.png)

1. 在 VS Code 的“AZURE 应用服务”资源管理器中，选择蓝色的向上箭头图标，将应用部署到 Azure。  （也可以从“命令面板”调用相同的命令 (**Ctrl**+**Shift**+**P**)，方法是键入“部署到 Web 应用”，并选择“Azure 应用服务:   部署到 Web 应用”）。

    ![部署到 Web 应用](containers/media/quickstart-nodejs/deploy.png)
        
1. 选择 *nodejs-docs-hello-world* 文件夹。

1. 根据要部署到的操作系统选择创建选项：

    - Linux：选择“创建新 Web 应用” 
    - Windows：选择“创建新 Web 应用...  高级”

1. 键入 Web 应用的全局唯一名称，然后按 **Enter**。 该名称必须在整个 Azure 中保持唯一，且只能使用字母数字字符（“A-Z”、“a-z”和“0-9”）和连字符（“-”）。

1. 如果以 Linux 为目标，请在出现提示时选择 Node.js 版本。 建议使用 **LTS** 版本。

1. 如果面向 Windows，请遵循附加提示：
    1. 选择“创建新的资源组”，然后输入资源组的名称，例如`AppServiceQS-rg`。 
    1. 选择 **Windows** 作为操作系统。
    1. 选择“创建新的应用服务计划”，输入该计划的名称（例如 `AppServiceQS-plan`），然后选择“F1 免费”作为定价层。  
    1. 当系统提示 Application Insights 时，选择“立即跳过”  。
    1. 选择你附近或想要访问的资源附近的区域。

1. 响应所有提示后，VS Code 将在其通知弹出窗口中显示正在为该应用创建的 Azure 资源。

    部署到 Linux 时，如果系统提示你更新配置以在目标 Linux 服务器上运行 `npm install`，请选择“是”。 

    ![在目标 Linux 服务器上更新配置的提示](containers/media/quickstart-nodejs/server-build.png)

1. 当系统提示你“始终将工作区 "nodejs-docs-hello-world" 部署到 (应用名称)”时，请选择“是”。   选择“是”就是告知 VS Code 在进行后续部署时自动以同一应用服务 Web 应用为目标。 

1. 如果部署到 Linux，请在部署完成后，选择提示中的“浏览网站”以查看全新部署的 Web 应用。  浏览器应会显示“Hello World!”

1. 如果部署到 Windows，必须先为 Web 应用设置 Node.js 版本号：

    1. 在 VS Code 中展开新应用服务的节点，右键单击“应用程序设置”，并选择“添加新设置...”：  

        ![添加应用设置的命令](containers/media/quickstart-nodejs/add-setting.png)

    1. 为设置键输入 `WEBSITE_NODE_DEFAULT_VERSION`。
    1. 为设置值输入 `10.15.2`。
    1. 右键单击应用服务的节点，并选择“重启” 

        ![重启应用服务的命令](containers/media/quickstart-nodejs/restart.png)

    1. 再次右键单击应用服务的节点，并选择“浏览网站”。 

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>排查 Azure 登录问题

如果在登录到 Azure 时出现错误“找不到名为 [订阅 ID] 的订阅”，原因可能是你使用了代理，因此无法访问 Azure API。  在终端中使用 `export` 以代理信息配置 `HTTP_PROXY` 和 `HTTPS_PROXY` 环境变量。

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

如果设置环境变量不能解决该问题，请选择上面的“我遇到了问题”按钮联系我们。 

### <a name="update-the-app"></a>更新应用

若要将更改部署到此应用，可以在 VS Code 中进行编辑，保存文件，然后使用前面所述的相同过程，只是这一次要选择现有的应用，而不是创建新应用。

## <a name="viewing-logs"></a>查看日志

可以在 VS Code 输出窗口中直接从应用查看日志输出（对 `console.log` 的调用）。

1. 在“AZURE 应用服务”资源管理器中右键单击应用节点，并选择“开始流式传输日志”。  

    ![开始流式传输日志](containers/media/quickstart-nodejs/view-logs.png)

1. 出现提示时，请选择启用日志记录并重启应用程序。 重启应用后，VS Code 输出窗口将会打开，其中包含与日志流建立的连接。 

    ![启用日志记录并重启](containers/media/quickstart-nodejs/enable-restart.png)

1. 几秒钟后，输出窗口将看到一条消息，指出已连接到日志流服务。 可以通过刷新浏览器中的页面来生成更多输出活动。

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

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
