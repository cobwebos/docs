---
title: "在 Azure 的 Linux 容器中创建 PHP Web 应用 | Microsoft Docs"
description: "数分钟内在 Azure App Service Web 应用中部署首个 PHP Hello World。"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 1b10636848198ab0ff46e6a492373ecf42a96202
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---
# <a name="create-a-php-web-app-in-a-linux-container-in-azure"></a>在 Azure 的 Linux 容器中创建 PHP Web 应用

[用于容器的 Web 应用](app-service-linux-intro.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本快速入门教程演示了如何将 PHP 应用部署到用于容器的 Azure Web 应用。 在 Cloud Shell 中使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 创建 Web 应用，并使用 Git 将 PHP 代码部署到 Web 应用。

![在 Azure 中运行的应用示例]](media/quickstart-php/hello-world-in-browser.png)

可以在 Mac、Windows 或 Linux 计算机上执行以下步骤。 

## <a name="prerequisites"></a>先决条件

完成本快速入门教程：

* [安装 Git](https://git-scm.com/)
* [安装 PHP](https://php.net)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下载示例

在终端窗口中运行以下命令，将示例应用程序克隆到本地计算机，并导航到包含示例代码的目录。

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>在本地运行应用

打开终端窗口并使用 `php` 命令启动内置 PHP Web 服务器，在本地运行应用程序。

```bash
php -S localhost:8080
```

打开 Web 浏览器并导航到 http://localhost:8080 处的示例应用。

此时会看到来自示例应用的 Hello World! 消息显示在页面中。

![本地运行应用的示例](media/quickstart-php/localhost-hello-world-in-browser.png)

在终端窗口中，按 **Ctrl+C** 退出 Web 服务器。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-linux-php-no-h.md)] 

浏览到该站点查看新建的 Web 应用。 将 _&lt;应用名称>_ 替换为唯一的应用名称。

```bash
http://<app name>.azurewebsites.net
```

![空 Web 应用页面](media/quickstart-php/app-service-web-service-created.png)

已在 Linux 容器中创建了空的新 Web 应用并启用了 Git 部署。

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到已部署的应用程序。

```bash
http://<app_name>.azurewebsites.net
```

PHP 示例代码在 Azure App Service Web 应用中运行。

![在 Azure 中运行应用的示例](media/quickstart-php/hello-world-in-browser.png)

**祝贺你！** 现已将第一个 PHP 应用部署到应用服务。

## <a name="update-locally-and-redeploy-the-code"></a>在本地更新并重新部署代码

使用本地文本编辑器打开 PHP 应用中的 `index.php` 文件，并对 `echo` 旁边的字符串中的文本稍微进行更改：

```php
echo "Hello Azure!";
```

提交在 Git 中所做的更改，然后将代码更改推送到 Azure。

```bash
git commit -am "updated output"
git push azure master
```

完成部署后，切换回**浏览到应用**步骤中打开的浏览器窗口，然后刷新页面。

![已更新的在 Azure 中运行应用的示例](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>管理新 Azure Web 应用

转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>管理创建的 Web 应用。

在左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

将看到 Web 应用的概述页。 在此处可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。

![Azure 门户中的应用服务页](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

左侧菜单提供用于配置应用的不同页面。 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [带 MySQL 的 PHP](tutorial-php-mysql-app.md)

