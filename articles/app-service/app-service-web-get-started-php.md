---
title: "在 Azure 中创建 PHP Web 应用 | Microsoft Docs"
description: "数分钟内在 Azure 应用服务 Web 应用中部署首个 PHP Hello World。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/13/2017
ms.author: cephalin;cfowler
ms.custom: mvc
ms.openlocfilehash: e38c8e7d6211c7c7b6bbf3a501ce53c2808ee0fc
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="create-a-php-web-app-in-azure"></a>在 Azure 中创建 PHP Web 应用

> [!NOTE]
> 本文将应用部署到 Windows 上的应用服务。 若要部署到基于 _Linux_ 的应用服务，请参阅[在基于 Linux 的应用服务中创建 PHP Web 应用](./containers/quickstart-php.md)。
>

[Azure Web 应用](app-service-web-overview.md)提供高度可缩放、自修补的 Web 托管服务。  本快速入门教程演示如何将 PHP 应用部署到 Azure Web 应用。 在 Cloud Shell 中使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 创建 Web 应用，并使用 [ZIP 文件](app-service-deploy-zip.md)将 PHP 代码示例部署到 Web 应用。

![在 Azure 中运行的应用示例]](media/app-service-web-get-started-php/hello-world-in-browser.png)

可以在 Mac、Windows 或 Linux 计算机上执行此处的步骤。 安装先决条件后，大约需要五分钟完成这些步骤。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本快速入门教程：

* <a href="https://php.net" target="_blank">安装 PHP</a>

## <a name="download-the-sample-locally"></a>将示例下载到本地

从 [https://github.com/Azure-Samples/php-docs-hello-world/archive/master.zip](https://github.com/Azure-Samples/php-docs-hello-world/archive/master.zip) 下载示例性的 Node.js 项目，然后解压缩 ZIP 存档。

在终端窗口中，导航到示例性 PHP 项目的根目录（包含 _index.php_ 的目录）。

## <a name="run-the-app-locally"></a>在本地运行应用

打开终端窗口并使用 `php` 命令启动内置 PHP Web 服务器，在本地运行应用程序。

```bash
php -S localhost:8080
```

打开 Web 浏览器并导航到 `http://localhost:8080` 处的示例应用。

此时会看到来自示例应用的 Hello World! 消息显示在页面中。

![本地运行应用的示例](media/app-service-web-get-started-php/localhost-hello-world-in-browser.png)

在终端窗口中，按 **Ctrl+C** 退出 Web 服务器。

[!INCLUDE [Create ZIP file](../../includes/app-service-web-create-zip.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>创建 Web 应用

在 Cloud Shell 中，使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 命令在 `myAppServicePlan` 应用服务计划中创建一个 Web 应用。 

在以下示例中，将 `<app_name>` 替换为全局唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。 运行时设置为 `PHP|7.0`。 若要查看所有受支持的运行时，请运行 [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes)。 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "PHP|7.0"
```

创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

浏览到新建的 Web 应用。 将 _&lt;应用名称>_ 替换为唯一的应用名称。

```bash
http://<app name>.azurewebsites.net
```

新 Web 应用应该如下所示：

![空 Web 应用页面](media/app-service-web-get-started-php/app-service-web-service-created.png)

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到已部署的应用程序。

```
http://<app_name>.azurewebsites.net
```

PHP 示例代码在 Azure 应用服务 Web 应用中运行。

![在 Azure 中运行应用的示例](media/app-service-web-get-started-php/hello-world-in-browser.png)

**祝贺你！** 现已将第一个 PHP 应用部署到应用服务。

## <a name="update-locally-and-redeploy-the-code"></a>在本地更新并重新部署代码

使用本地文本编辑器打开 PHP 应用中的 `index.php` 文件，并对 `echo` 旁边的字符串中的文本稍微进行更改：

```php
echo "Hello Azure!";
```

在本地终端窗口中，导航到应用程序的根目录，为更新的项目创建新的 ZIP 文件。

```
# Bash
zip -r myUpdatedAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myUpdatedAppFiles.zip
``` 

将此新的 ZIP 文件部署到应用服务，使用的步骤与[上传 ZIP 文件](#upload-the-zip-file)中的相同。

切换回在“浏览到应用”步骤中打开的浏览器窗口，然后刷新页面。

![已更新的在 Azure 中运行应用的示例](media/app-service-web-get-started-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>管理新 Azure Web 应用

转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>管理创建的 Web 应用。

在左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/app-service-web-get-started-php/php-docs-hello-world-app-service-list.png)

将看到 Web 应用的概述页。 在此处可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。

![Azure 门户中的应用服务页](media/app-service-web-get-started-php/php-docs-hello-world-app-service-detail.png)

左侧菜单提供用于配置应用的不同页面。 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [带 MySQL 的 PHP](app-service-web-tutorial-php-mysql.md)
