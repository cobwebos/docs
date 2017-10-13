---
title: "在用于容器的 Azure Web 应用中运行自定义 Docker 中心映像 | Microsoft Docs"
description: "如何对用于容器的 Azure Web 应用使用自定义 Docker 映像。"
keywords: "azure 应用服务、web 应用、linux、docker、容器"
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 28b0b50c68fa7c16a354fd16b7dfd930436c6b4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-custom-docker-hub-image-in-azure-web-app-for-containers"></a>在用于容器的 Azure Web 应用中运行自定义 Docker 中心映像 #

在 Linux 上，应用服务提供预定义的应用程序堆栈，并支持特定版本，例如 PHP 7.0 和 Node.js 4.5。 还可使用自定义 Docker 映像将 Web 应用部署到尚未在 Azure 中定义的应用程序堆栈。 本快速入门介绍了如何创建 Web 应用并向其部署 Python 映像。 使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 创建 Web 应用。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)] 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)] 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)] 

## <a name="create-a-web-app"></a>创建 Web 应用

使用 [az webapp create](/cli/azure/webapp#create) 命令在 `myAppServicePlan` 应用服务计划中创建 [Web 应用](../app-service-web-overview.md)。 不要忘记将 `<app name>` 替换为唯一的应用名称。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name elnably/dockerimagetest
```

在上述命令中，`--deployment-container-image-name` 指向公共 Docker 中心映像 [https://hub.docker.com/r/elnably/dockerimagetest/](https://hub.docker.com/r/elnably/dockerimagetest/)。 可在 [https://github.com/rachelappel/docker-image](https://github.com/rachelappel/docker-image) 处检查其内容。

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
> [在 Azure 中构建 Docker Python 和 PostgreSQL Web 应用](tutorial-docker-python-postgresql-app.md)
