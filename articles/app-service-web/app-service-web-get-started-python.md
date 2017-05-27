---
title: "在 Azure 中不到 5 分钟创建你的第一个 Python Web 应用 | Microsoft Docs"
description: "在应用服务 Web 应用中快速部署第一个 Python Hello World 应用。"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: df34052acc401fb5bb1e3f808c649c0ea0bcf33c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017


---
# <a name="create-a-python-application-on-web-app"></a>在 Web 应用中创建 Python 应用程序

本快速入门教程逐步讲解如何开发 Python 应用并将其部署到 Azure。 我们将使用 Azure App Service 运行该应用，再使用 Azure CLI 在其中创建并配置一个新的 Web 应用。 然后，我们使用 git 将该 Python 应用部署到 Azure。

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

可以在 Mac、Windows 或 Linux 计算机上执行以下步骤。 大约 5 分钟内应可完成下面的所有步骤。

## <a name="prerequisites"></a>先决条件

运行本示例之前，请在本地安装以下必备组件：

1. [下载并安装 git](https://git-scm.com/)
1. [下载并安装 Python](https://www.python.org/downloads/)
1. 下载并安装 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="download-the-sample"></a>下载示例

将 Hello World 示例应用存储库克隆到本地计算机。

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

> [!TIP]
> 此外，也可以[下载示例](https://github.com/Azure-Samples/Python-docs-hello-world/archive/master.zip) zip 文件并将其解压缩。

切换到包含示例代码的目录。

```bash
cd Python-docs-hello-world
```

## <a name="run-the-app-locally"></a>在本地运行应用

打开一个终端窗口并使用针对该示例的 `Python` 命令行启动内置的 Python Web 服务器，在本地运行该应用程序。

```bash
python main.py
```

打开 Web 浏览器并导航到该示例。

```bash
http://localhost:5000
```

页面中会显示该示例应用发出的 **Hello World** 消息。

![localhost-hello-world-in-browser](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

在终端窗口中，按 **Ctrl+C** 退出 Web 服务器。

## <a name="log-in-to-azure"></a>登录 Azure

现在，我们将在终端窗口中使用 Azure CLI 2.0 创建所需的资源用于在 Azure 中托管该 Python 应用。 使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>配置部署用户

对于 FTP 和本地 Git 部署，需在服务器上配置一个部署用户，用于对部署进行身份验证。 创建部署用户是一次性的配置。请记下用户名和密码，因为要在下面的步骤中用到。

> [!NOTE]
> 在 Web 应用中进行 FTP 和本地 Git 部署时需要一个部署用户。
> `username` 和 `password` 是帐户级别的凭据，因此不同于 Azure 订阅凭据。 **只需创建这些凭据一次**。
>

使用 [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 命令创建帐户级别的凭据。

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源（例如 Web 应用、数据库和存储帐户）的逻辑容器。

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>创建 Azure 应用服务

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令创建应用服务计划。

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

以下示例使用**免费**定价层创建名为 `quickStartPlan` 的应用服务计划。

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

创建应用服务计划后，Azure CLI 将显示类似于以下示例的信息。

```json
{
"appServicePlanName": "quickStartPlan",
"geoRegion": "North Europe",
"id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
"kind": "app",
"location": "North Europe",
"maximumNumberOfWorkers": 1,
"name": "quickStartPlan",
"provisioningState": "Succeeded",
"resourceGroup": "myResourceGroup",
"sku": {
  "capacity": 0,
  "family": "F",
  "name": "F1",
  "size": "F1",
  "tier": "Free"
},
"status": "Ready",
"type": "Microsoft.Web/serverfarms",
}
```

## <a name="create-a-web-app"></a>创建 Web 应用

创建应用服务计划后，请在 `quickStartPlan` 应用服务计划中创建 Web 应用。 该 Web 应用提供托管空间用于部署代码，并提供一个 URL 用于查看已部署的应用程序。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令创建该 Web 应用。

在以下命令中，请将出现的 `<app_name>` 占位符替换为你自己的唯一应用名称。 `<app_name>` 将用作 Web 应用的默认 DNS 站点，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 稍后，可以先将任何自定义 DNS 条目映射到 Web 应用，然后向用户公开该条目。

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

创建 Web 应用后，Azure CLI 将显示类似于以下示例的信息。

```json
{
  "clientAffinityEnabled": true,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "hostNames": [
    "<app_name>.azurewebsites.net"
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
  "kind": "app",
  "location": "North Europe",
  "outboundIpAddresses": "13.69.190.80,13.69.191.239,13.69.186.193,13.69.187.34",
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

![app-service-web-service-created](media/app-service-web-get-started-python/app-service-web-service-created.png)

现在，我们已在 Azure 中创建一个空的新 Web 应用。 接下来，将 Web 应用配置为使用 Python 并将其部署到 Python 中。

## <a name="configure-to-use-python"></a>配置为使用 Python

使用 [az appservice web config update](/cli/azure/app-service/web/config#update) 命令将 Web 应用配置为使用 Python 版本 `3.4`。

> [!TIP]
> 以这种方式设置 Python 版本会使用平台提供的默认容器。如果你想要使用自己的容器，请参阅 [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) 命令的 CLI 参考文章。

```azurecli
az appservice web config update --python-version 3.4 --name <app-name> --resource-group myResourceGroup
```

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

推送到 Azure 远程功能以部署应用程序。 系统将提示你输入前面在创建部署用户期间提供的密码。

```azurecli
git push azure master
```

在部署期间，Azure 应用服务会向 Git 告知其进度。

```bash
Counting objects: 18, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (16/16), done.
Writing objects: 100% (18/18), 4.31 KiB | 0 bytes/s, done.
Total 18 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '44e74fe7dd'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling python deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'main.py'
remote: Copying file: 'README.md'
remote: Copying file: 'requirements.txt'
remote: Copying file: 'virtualenv_proxy.py'
remote: Copying file: 'web.2.7.config'
remote: Copying file: 'web.3.4.config'
remote: Detected requirements.txt.  You can skip Python specific steps with a .skipPythonDeployment file.
remote: Detecting Python runtime from site configuration
remote: Detected python-3.4
remote: Creating python-3.4 virtual environment.
remote: .................................
remote: Pip install requirements.
remote: Successfully installed Flask click itsdangerous Jinja2 Werkzeug MarkupSafe
remote: Cleaning up...
remote: .
remote: Overwriting web.config with web.3.4.config
remote:         1 file(s) copied.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到已部署的应用程序。

```bash
http://<app_name>.azurewebsites.net
```

此时，显示 Hello World 消息的页面正在使用作为 Azure 应用服务 Web 应用运行的 Python 代码运行。

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

## <a name="updating-and-deploying-the-code"></a>更新和部署代码

使用本地文本编辑器打开 Python 应用中的 `main.py` 文件，然后对 `return` 语句旁边的字符串中的文本稍微进行更改：

```python
return 'Hello, Azure!'
```

提交在 git 中所做的更改，然后将代码更改推送到 Azure。

```bash
git commit -am "updated output"
git push azure master
```

完成部署后，切换回在“浏览到应用步骤”中打开的浏览器窗口，然后点击“刷新”。

![hello-azure-in-browser](media/app-service-web-get-started-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>管理新 Azure Web 应用

转到 Azure 门户，查看刚刚创建的 Web 应用。

为此，请登录到 [https://portal.azure.com](https://portal.azure.com)。

从左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/app-service-web-get-started-python/app-service-list.png)

现已进入 Web 应用的_边栏选项卡_（水平打开的门户页）。

默认情况下，Web 应用的边栏选项卡显示“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 边栏选项卡左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务边栏选项卡](media/app-service-web-get-started-python/app-service-detail.png)

边栏选项卡中的这些选项卡显示了可添加到 Web 应用的许多强大功能。 以下列表只是列出了一部分可用的功能：

* 映射自定义 DNS 名称
* 绑定自定义 SSL 证书
* 配置持续部署
* 扩展和缩减
* 添加用户身份验证

**祝贺你！** 现已将第一个 Python 应用部署到应用服务。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览示例 Web 应用 CLI 脚本](app-service-cli-samples.md)

