---
title: 创建 Node.js Web 应用 - Azure 应用服务 | Microsoft Docs
description: 数分钟内在 Azure 应用服务 Web 应用中部署第一个 Node.js Hello World。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 237f498d1ebe2b402c86f1a4aed66a7ed443ccfa
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56653651"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>在 Azure 中创建 Node.js Web 应用

> [!NOTE]
> 本文将应用部署到 Windows 上的应用服务。 若要部署到基于 _Linux_ 的应用服务，请参阅[在基于 Linux 的 Azure 应用服务中创建 Node.js Web 应用](./containers/quickstart-nodejs.md)。
>

[Azure 应用服务](overview.md)提供高度可缩放、自修补的 Web 托管服务。  本快速入门演示如何将 Node.js 应用部署到 Azure 应用服务中。 你会使用 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 创建 Web 应用，但是也可以使用 [Azure CLI](/cli/azure/install-azure-cli) 在本地运行这些命令。 使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 命令将示例 Node.js 代码部署到 Web 应用。  

![在 Azure 中运行应用的示例](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

可以在 Mac、Windows 或 Linux 计算机上执行此处的步骤。 完成这些步骤需要大约三分钟。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>下载示例

在 Cloud Shell 中，创建一个 quickstart 目录，然后切换到该目录。

```azurecli-interactive
mkdir quickstart

cd quickstart
```

接下来请运行以下命令，将示例应用存储库克隆到快速入门目录。

```azurecli-interactive
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

运行时，该命令会显示类似于以下示例的信息：

```bash
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
```

> [!NOTE]
> 示例 index.js 将侦听端口设置为 process.env.PORT。 此环境变量是由应用服务分配的。
>

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)]

## <a name="create-a-web-app"></a>创建 Web 应用

在 Cloud Shell 中，使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 命令在 `myAppServicePlan` 应用服务计划中创建一个 Web 应用。

在以下示例中，将 `<app_name>` 替换为全局唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
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

### <a name="set-nodejs-runtime"></a>设置 Node.js 运行时

将 Node 运行时设置为 10.14.1。 若要查看所有受支持的运行时，请运行 [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)。

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

浏览到新建的 Web 应用。 将 `<app_name>` 替换为唯一应用名称。

```
http://<app_name>.azurewebsites.net
```

新 Web 应用应该如下所示：![空 Web 应用页面](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

## <a name="deploy-zip-file"></a>部署 ZIP 文件

在 Cloud Shell 中，导航到应用程序的根目录，为示例项目创建新的 ZIP 文件。

```azurecli-interactive
cd nodejs-docs-hello-world  

zip -r myUpdatedAppFiles.zip *.*
```

使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 命令将 ZIP 文件部署到 Web 应用。  

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

此命令将 zip 文件中的文件和目录部署到默认的应用服务应用程序文件夹 (`\home\site\wwwroot`) 并重启应用。 如果配置了任何其他自定义生成过程，则也会运行该过程。 有关详细信息，请参阅 [Kudu 文档](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到已部署的应用程序。

```
http://<app_name>.azurewebsites.net
```

Node.js 示例代码在 Azure 应用服务 Web 应用中运行。

![在 Azure 中运行应用的示例](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

> [!NOTE]
> 在 Azure 应用服务中，此应用在 IIS 中使用 [iisnode](https://github.com/Azure/iisnode) 运行。 为了让应用能够使用 iisnode 运行，根应用目录包含一个 web.config 文件。 此文件可以由 IIS 读取，与 iisnode 相关的设置记录在 [iisnode GitHub 存储库](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config)中。

祝贺你！ 现已将第一个 Node.js 应用部署到应用服务。

## <a name="update-and-redeploy-the-code"></a>更新并重新部署代码

在 Cloud Shell 中，键入 `code index.js` 以打开 Cloud Shell 编辑器。

![编写 index.js 的代码](media/app-service-web-get-started-nodejs-poc/code-indexjs.png)

对 `response.end` 调用中的文本稍作更改：

```javascript
response.end("Hello Azure!");
```

保存更改并退出编辑器。 使用命令 `^S` 来保存，使用 `^Q` 来退出。

使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 命令创建 ZIP 文件并部署它。  

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip *.*

az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

切换回在“浏览到应用”步骤中打开的浏览器窗口，然后刷新页面。

![已更新的在 Azure 中运行应用的示例](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>管理新的 Azure 应用

转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>管理创建的 Web 应用。

在左侧菜单中单击“应用程序服务”，然后单击 Azure 应用的名称。

![在门户中导航到 Azure 应用](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

这里我们可以看到 Web 应用的概述页。 并可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。

![Azure 门户中的应用服务页](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

左侧菜单提供了用于配置应用的不同页面。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 Node.js 与 MongoDB 配合使用](app-service-web-tutorial-nodejs-mongodb-app.md)
