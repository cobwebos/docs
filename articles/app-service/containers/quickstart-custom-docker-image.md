---
title: "在用于容器的 Azure Web 应用中运行自定义 Docker 中心映像 | Microsoft Docs"
description: "如何对用于容器的 Azure Web 应用使用自定义 Docker 映像。"
keywords: "azure 应用服务、web 应用、linux、docker、容器"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: cephalin;wesmc
ms.custom: mvc
ms.openlocfilehash: 7f6ed6d52bea1faec9dbed96a8d7aef020aea5d9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="run-a-custom-docker-hub-image-in-azure-web-app-for-containers"></a>在用于容器的 Azure Web 应用中运行自定义 Docker 中心映像

在 Linux 上，应用服务提供预定义的应用程序堆栈，并支持特定版本，例如 PHP 7.0 和 Node.js 4.5。 还可使用自定义 Docker 映像，于尚未在 Azure 中定义的应用程序堆栈中运行 Web 应用。 本快速入门介绍了如何创建 Web 应用并向其部署[官方 Nginx Docker 映像](https://hub.docker.com/r/_/nginx/)。 使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 创建 Web 应用。

![在 Azure 中运行应用的示例](media/quickstart-custom-docker-image/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>为容器创建 Web 应用

使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 命令在 `myAppServicePlan` 应用服务计划中创建一个 [Web 应用](../app-service-web-overview.md)。 不要忘记将 `<app name>` 替换为唯一的应用名称。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name nginx
```

在上述命令中，`--deployment-container-image-name` 指向公共 Docker 中心映像 [https://hub.docker.com/r/_/nginx/](https://hub.docker.com/r/_/nginx/)。

创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到以下 URL。

```bash
http://<app_name>.azurewebsites.net
```

![在 Azure 中运行应用的示例](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**祝贺你！** 已将自定义 Docker 映像部署到用于容器的 Web 应用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用自定义 Docker 映像](tutorial-custom-docker-image.md)
