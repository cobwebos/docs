---
title: "在 Azure 中创建 Python Web 应用 | Microsoft Docs"
description: "数分钟内在 Azure 应用服务 Web 应用中部署首个 Python Hello World。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/26/2017
ms.author: cephalin;cfowler
ms.custom: mvc, devcenter
ms.openlocfilehash: ae410c7fabac6d23a69922804a0a87fde63594a2
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="create-a-python-web-app-in-azure"></a>在 Azure 中创建 Python Web 应用

[Azure Web 应用](app-service-web-overview.md)提供高度可缩放、自修补的 Web 托管服务。  本快速入门教程演示如何开发 Python 应用并将其部署到 Azure Web 应用。 使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 创建 Web 应用，并使用 Git 将 Python 代码示例部署到 Web 应用。

![在 Azure 中运行应用的示例](media/app-service-web-get-started-python/hello-world-in-browser.png)

可以在 Mac、Windows 或 Linux 计算机上执行以下步骤。 安装先决条件后，大约需要五分钟完成这些步骤。

## <a name="prerequisites"></a>先决条件

完成本教程：

1. [安装 Git](https://git-scm.com/)
1. [安装 Python](https://www.python.org/downloads/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下载示例

在终端窗口中，运行以下命令，将示例应用存储库克隆到本地计算机。

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

切换到包含示例代码的目录。

```bash
cd python-docs-hello-world
```

## <a name="run-the-app-locally"></a>在本地运行应用

使用 `pip` 安装所需包。

```bash
pip install -r requirements.txt
```

打开终端窗口并使用 `Python` 命令启动内置 Python Web 服务器，在本地运行应用程序。

```bash
python main.py
```

打开 Web 浏览器并导航到 `http://localhost:5000` 处的示例应用。

页面中会显示该示例应用发出的 **Hello World** 消息。

![本地运行应用的示例](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

在终端窗口中，按 **Ctrl+C** 退出 Web 服务器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-python-no-h.md)]

浏览到新建的 Web 应用。 将 _&lt;应用名称>_ 替换为唯一的应用名称。

```bash
http://<app name>.azurewebsites.net
```

![空 Web 应用页面](media/app-service-web-get-started-python/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)]

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

Python 示例代码在 Azure 应用服务 Web 应用中运行。

![在 Azure 中运行应用的示例](media/app-service-web-get-started-python/hello-world-in-browser.png)

**祝贺你！** 现已将第一个 Python 应用部署到应用服务。

## <a name="update-and-redeploy-the-code"></a>更新并重新部署代码

使用本地文本编辑器在 Python 应用中打开 `main.py` 文件，然后对 `return` 语句旁边的文本稍微进行更改：

```python
return 'Hello, Azure!'
```

在本地终端窗口中，提交在 Git 中所做的更改，然后将代码更改推送到 Azure。

```bash
git commit -am "updated output"
git push azure master
```

完成部署后，切换回[浏览到应用](#browse-to-the-app)步骤中打开的浏览器窗口，然后刷新页面。

![已更新的在 Azure 中运行应用的示例](media/app-service-web-get-started-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>管理新 Azure Web 应用

转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>管理创建的 Web 应用。

在左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

将看到 Web 应用的概述页。 在此处可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。

![Azure 门户中的应用服务边栏选项卡](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

左侧菜单提供用于配置应用的不同页面。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure Web 应用](app-service-web-tutorial-custom-domain.md)
