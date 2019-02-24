---
title: 在 Linux 上创建 Python 应用 - Azure 应用服务 | Microsoft Docs
description: 数分钟内在 Linux 上的 Azure 应用服务中部署第一个 Python hello world 应用。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d0c51f5d4757c35319cc3f80d09c9fb1a0e1cf69
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313911"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>在 Linux 上的 Azure 应用服务（预览）中创建 Python 应用

[Linux 应用服务](app-service-linux-intro.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本快速入门展示了如何使用 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 在 Linux 应用服务中，在内置 Python 映像（预览）之上部署 Python 应用。

可以使用 Mac、Windows 或 Linux 计算机执行本文中的步骤。

![在 Azure 中运行应用的示例](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>下载示例

在 Cloud Shell 中，创建一个 quickstart 目录，然后切换到该目录。

```bash
mkdir quickstart

cd quickstart
```

接下来请运行以下命令，将示例应用存储库克隆到快速入门目录。

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

运行时，该命令会显示类似于以下示例的信息：

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>创建 Web 应用

切换到包含示例代码的目录并运行 `az webapp up` 命令。

在以下示例中，请将 <app_name> 替换为一个唯一的应用名称。

```bash
cd python-docs-hello-world

az webapp up -n <app_name>
```

此命令可能需要花费几分钟时间运行。 运行时，该命令会显示类似于以下示例的信息：

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

`az webapp up` 命令执行以下操作：

- 创建一个默认的资源组。

- 创建一个默认的应用服务计划。

- 创建一个采用指定名称的应用。

- [使用 Zip](https://docs.microsoft.com/azure/app-service/deploy-zip) 将文件从当前工作目录部署到应用。

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到已部署的应用程序。

```bash
http://<app_name>.azurewebsites.net
```

Python 示例代码在包含内置映像的 Linux 上的应用服务中运行。

![在 Azure 中运行应用的示例](media/quickstart-python/hello-world-in-browser.png)

祝贺你！ 现已将第一个 Python 应用部署到 Linux 应用服务。

## <a name="update-locally-and-redeploy-the-code"></a>在本地更新并重新部署代码

在 Cloud Shell 中，键入 `code application.py` 以打开 Cloud Shell 编辑器。

![对 application.py 进行编码](media/quickstart-python/code-applicationpy.png)

 对 `return` 调用中的文本稍作更改：

```python
return "Hello Azure!"
```

保存更改并退出编辑器。 使用命令 `^S` 来保存，使用 `^Q` 来退出。

现在，你将重新部署应用。 将 `<app_name>` 替换为你的应用。

```bash
az webapp up -n <app_name>
```

完成部署后，切换回**浏览到应用**步骤中打开的浏览器窗口，然后刷新页面。

![已更新的在 Azure 中运行应用的示例](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>管理新的 Azure 应用

转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>管理已创建的应用。

在左侧菜单中单击“应用程序服务”，然后单击 Azure 应用的名称。

![在门户中导航到 Azure 应用](./media/quickstart-python/app-service-list.png)

这里我们可以看到应用的“概述”页。 并可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。

![Azure 门户中的应用服务页](media/quickstart-python/app-service-detail.png)

左侧菜单提供了用于配置应用的不同页面。 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

Linux 上的应用服务中内置的 Python 映像目前处于预览状态，你可以自定义用于启动应用的命令。 还可以改用自定义容器创建生产 Python 应用。

> [!div class="nextstepaction"]
> [将 Python 与 PostgreSQL 配合使用](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [配置自定义启动命令](how-to-configure-python.md#customize-startup-command)

> [!div class="nextstepaction"]
> [故障排除](how-to-configure-python.md#troubleshooting)

> [!div class="nextstepaction"]
> [使用自定义映像](tutorial-custom-docker-image.md)
