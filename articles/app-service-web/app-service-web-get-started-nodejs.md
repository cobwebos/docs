---
title: "在 Azure Web 应用中创建 Node.js 应用程序 | Microsoft Docs"
description: "在应用服务 Web 应用中快速部署第一个 Node.js Hello World 应用。"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/05/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ced6f54603120d8832ee417b02b6673f80a99613
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="create-a-nodejs-application-on-web-app"></a>在 Web 应用中创建 Node.js 应用程序

本快速入门教程逐步讲解如何开发 Node.js 应用并将其部署到 Azure。 我们将使用 [Azure 应用服务计划](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)运行该应用，然后使用 Azure CLI 在其中创建并配置一个新的 Web 应用。 然后，我们使用 git 将该 Node.js 应用部署到 Azure。

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

可以在 Mac、Windows 或 Linux 计算机上执行以下步骤。 大约 5 分钟内应可完成下面的所有步骤。

## <a name="prerequisites"></a>先决条件

创建此示例之前，请下载并安装以下项：

* [Git](https://git-scm.com/)
* [Node.js 和 NPM](https://nodejs.org/)
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下载示例

将 Hello World 示例应用存储库克隆到本地计算机。

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

切换到包含示例代码的目录。

```bash
cd nodejs-docs-hello-world
```

## <a name="run-the-app-locally"></a>在本地运行应用

打开一个终端窗口并使用针对该示例的 `npm start` 脚本启动内置的 Node.js http 服务器，在本地运行该应用程序。

```bash
npm start
```

打开 Web 浏览器并导航到该示例。

```bash
http://localhost:1337
```

页面中会显示该示例应用发出的 **Hello World** 消息。

![localhost-hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

在终端窗口中，按 **Ctrl+C** 退出 Web 服务器。

## <a name="log-in-to-azure"></a>登录 Azure

现在，我们将在终端窗口中使用 Azure CLI 2.0 创建所需的资源用于在 Azure 中托管该 Node.js 应用。 使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```azurecli
az login
```

<!-- ## Configure a Deployment User -->
[!INCLUDE [login-to-azure](../../includes/configure-deployment-user.md)]

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源（例如 Web 应用、数据库和存储帐户）的逻辑容器。

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service-plan"></a>创建 Azure 应用服务计划

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令创建“免费”的[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。

<!--
 An App Service plan represents the collection of physical resources used to ..
-->
[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

以下示例使用**免费**定价层创建名为 `quickStartPlan` 的应用服务计划。

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

创建应用服务计划后，Azure CLI 将显示类似于以下示例的信息：

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "location": "West Europe",
    "sku": {
    "capacity": 1,
    "family": "S",
    "name": "S1",
    "tier": "Standard"
    },
    "status": "Ready",
    "type": "Microsoft.Web/serverfarms"
}
```

## <a name="create-a-web-app"></a>创建 Web 应用

创建应用服务计划后，请在 `quickStartPlan` 应用服务计划中创建一个 [Web 应用](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview)。 该 Web 应用提供托管空间用于部署代码，并提供一个 URL 用于查看已部署的应用程序。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令创建该 Web 应用。

在以下命令中，请将出现的 `<app_name>` 占位符替换为你自己的唯一应用名称。 在默认 DNS 站点中，将 `<app_name>` 用于 Web 应用。 如果 `<app_name>` 不唯一，你将收到友好错误消息“具有给定名称 <app_name> 的网站已存在”。

<!-- removed per https://github.com/Microsoft/azure-docs-pr/issues/11878
You can later map any custom DNS entry to the web app before you expose it to your users.
-->

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

创建 Web 应用后，Azure CLI 将显示类似于以下示例的信息。

```json
{
    "clientAffinityEnabled": true,
    "defaultHostName": "<app_name>.azurewebsites.net",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
    "isDefaultContainer": null,
    "kind": "app",
    "location": "West Europe",
    "name": "<app_name>",
    "repositorySiteName": "<app_name>",
    "reserved": true,
    "resourceGroup": "myResourceGroup",
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "state": "Running",
    "type": "Microsoft.Web/sites",
}
```

浏览到该站点查看新建的 Web 应用。

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

现在，我们已在 Azure 中创建一个空的新 Web 应用。

## <a name="configure-local-git-deployment"></a>配置本地 Git 部署

可以通过不同的方法部署到 Web 应用，包括 FTP、本地 Git，以及 GitHub、Visual Studio Team Services 和 Bitbucket。

使用 [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 命令配置对 Web 应用的本地 git 访问。

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

复制终端的输出，因为下一步骤将要用到。

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

将 Azure 远程功能添加到本地 Git 存储库。

```bash
git remote add azure <paste-previous-command-output-here>
```

推送到 Azure 远程功能以部署应用。 系统将提示你输入前面在创建部署用户时提供的密码。 请确保输入在[配置部署用户](#configure-a-deployment-user)中创建的密码，而不是用于登录到 Azure 门户的密码。

```bash
git push azure master
```

在部署期间，Azure 应用服务会向 Git 告知其进度。

```bash
Counting objects: 23, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (23/23), 3.71 KiB | 0 bytes/s, done.
Total 23 (delta 8), reused 7 (delta 1)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'bf114df591'.
remote: Generating deployment script.
remote: Generating deployment script for node.js Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling node.js deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.js'
remote: Copying file: 'package.json'
remote: Copying file: 'process.json'
remote: Deleting file: 'hostingstart.html'
remote: Ignoring: .git
remote: Using start-up script index.js from package.json.
remote: Node.js versions available on the platform are: 4.4.7, 4.5.0, 6.2.2, 6.6.0, 6.9.1.
remote: Selected node.js version 6.9.1. Use package.json file to choose a different version.
remote: Selected npm version 3.10.8
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net:443/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到已部署的应用程序。

```bash
http://<app_name>.azurewebsites.net
```

此时，显示 Hello World 消息的页面正在使用作为 Azure 应用服务 Web 应用运行的 Node.js 代码运行。

## <a name="updating-and-deploying-the-code"></a>更新和部署代码

使用本地文本编辑器打开 Node.js 应用中的 `index.js` 文件，然后对 `response.end` 调用中的文本稍微进行更改：

```nodejs
response.end("Hello Azure!");
```

提交在 git 中所做的更改，然后将代码更改推送到 Azure。

```bash
git commit -am "updated output"
git push azure master
```

完成部署后，切换回在“浏览到应用”步骤中打开的浏览器窗口，然后点击“刷新”。

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>管理新 Azure Web 应用

转到 Azure 门户，查看刚刚创建的 Web 应用。

为此，请登录到 [https://portal.azure.com](https://portal.azure.com)。

从左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

现已进入 Web 应用的_边栏选项卡_（水平打开的门户页）。

默认情况下，Web 应用的边栏选项卡显示“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 边栏选项卡左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务边栏选项卡](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

边栏选项卡中的这些选项卡显示了可添加到 Web 应用的许多强大功能。 以下列表只是列出了一部分可用的功能：

* 映射自定义 DNS 名称
* 绑定自定义 SSL 证书
* 配置持续部署
* 扩展和缩减
* 添加用户身份验证

**祝贺你！** 现已将第一个 Node.js 应用部署到应用服务。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

> [!div class="nextstepaction"]
> [浏览示例 Web 应用 CLI 脚本](app-service-cli-samples.md)

