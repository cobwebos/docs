---
title: 在 Linux 上的 Azure 应用服务中创建 Node.js | Microsoft Docs
description: 数分钟内在 Linux 上的 Azure 应用服务中部署第一个 Node.js Hello World。
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/07/2017
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: eb1c769e034f37d05de63896f65290db79103637
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293898"
---
# <a name="create-a-nodejs-web-app-in-azure-app-service-on-linux"></a>在 Linux 上的 Azure 应用服务中创建 Node.js Web 应用

> [!NOTE]
> 本文将应用部署到基于 Linux 的应用服务。 若要部署到基于 _Windows_ 的应用服务，请参阅[在 Azure 中创建 Node.js Web 应用](../app-service-web-get-started-nodejs.md)。
>

[Linux 应用服务](app-service-linux-intro.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本快速入门展示了如何使用 [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) 将 Node.js 应用部署到 Linux 上的应用服务。

你将在 Cloud Shell 中完成本快速入门，但是也可以使用 [Azure CLI](/cli/azure/install-azure-cli) 在本地运行这些命令。

![在 Azure 中运行应用的示例](media/quickstart-nodejs/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-web-app-extension-for-cloud-shell"></a>安装 Cloud Shell 的 Web 应用扩展

若要完成本快速入门，需要添加 [az Web 应用扩展](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az-extension-add)。 如果该扩展已安装，则应将其更新为最新版本。 若要更新该 Web 应用扩展，请键入 `az extension update -n webapp`。

若要安装该 Web 应用扩展，请运行以下命令：

```bash
az extension add -n webapp
```

安装该扩展后，Cloud Shell 会显示类似于以下示例的信息：

```bash
The installed extension 'webapp' is in preview.
```

## <a name="download-the-sample"></a>下载示例

在 Cloud Shell 中，创建一个 quickstart 目录，然后切换到该目录。

```bash
mkdir quickstart

cd quickstart
```

接下来，运行以下命令将示例应用存储库克隆到 quickstart 目录。

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

运行时，该命令会显示类似于以下示例的信息：

```bash
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
````

## <a name="create-a-web-app"></a>创建 Web 应用

切换到包含示例代码的目录并运行 `az webapp up` 命令。

在以下示例中，请将 <app_name> 替换为一个唯一的应用名称。

```bash
cd nodejs-docs-hello-world

az webapp up -n <app_name>
```

此命令可能需要花费几分钟时间运行。 运行时，该命令会显示类似于以下示例的信息：

```json
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Updating app settings to enable build after deployment
Creating zip with contents of dir /home/username/quickstart/nodejs-docs-hello-world ...
Preparing to deploy and build contents to app.
Fetching changes.

Generating deployment script.
Generating deployment script.
Generating deployment script.
Running deployment command...
Running deployment command...
Running deployment command...
Deployment successful.
All done.
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "STANDARD",
  "src_path": "/home/username/quickstart/nodejs-docs-hello-world ",
  "version_detected": "6.9",
  "version_to_create": "node|6.9"
}
```

`az webapp up` 命令执行以下操作：

- 创建一个默认的资源组。

- 创建一个默认的应用服务计划。

- 创建一个采用指定名称的应用。

- [使用 Zip](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-zip) 将文件从当前工作目录部署到 Web 应用。

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到已部署的应用程序。 将 <app_name> 替换为你的 Web 应用名称。

```bash
http://<app_name>.azurewebsites.net
```

Node.js 示例代码在包含内置映像的 Web 应用中运行。

![在 Azure 中运行应用的示例](media/quickstart-nodejs/hello-world-in-browser.png)

**祝贺你！** 现已将第一个 Node.js 应用部署到 Linux 应用服务。

## <a name="update-and-redeploy-the-code"></a>更新并重新部署代码

在 Cloud Shell 中，键入 `nano index.js` 以打开 nano 文本编辑器。

![Nano index.js](media/quickstart-nodejs/nano-indexjs.png)

 对 `response.end` 调用中的文本稍作更改：

```nodejs
response.end("Hello Azure!");
```

保存更改并退出 nano。 使用命令 `^O` 来保存，使用 `^X` 来退出。

现在，你将重新部署应用。 将 `<app_name>` 替换为你的 Web 应用。

```bash
az webapp up -n <app_name>
```

完成部署后，切换回**浏览到应用**步骤中打开的浏览器窗口，然后刷新页面。

![已更新的在 Azure 中运行应用的示例](media/quickstart-nodejs/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>管理新 Azure Web 应用

转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>管理创建的 Web 应用。

在左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/quickstart-nodejs/nodejs-docs-hello-world-app-service-list.png)

将看到 Web 应用的概述页。 在此处，可以完成基本的管理任务，例如浏览、停止、启动、重启和删除。

![Azure 门户中的应用服务页](media/quickstart-nodejs/nodejs-docs-hello-world-app-service-detail.png)

左侧菜单提供用于配置应用的不同页面。

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，在资源组中创建了 Azure 资源。 如果预计将来不需要这些资源，请从 Cloud Shell 中删除资源组。 如果已修改了区域，请将资源组名称 `appsvc_rg_Linux_CentralUS` 更新为特定于你的应用的资源组。

```azurecli-interactive
az group delete --name appsvc_rg_Linux_CentralUS
```

此命令可能需要花费一分钟时间运行。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 Node.js 与 MongoDB 配合使用](tutorial-nodejs-mongodb-app.md)
