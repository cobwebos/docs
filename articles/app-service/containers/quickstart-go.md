---
title: "在用于容器的 Azure Web 应用中部署 Go 应用 | Microsoft Docs"
description: "如何为用于容器的 Azure Web 应用部署运行 Go 应用程序的 Docker 映像。"
keywords: "azure 应用服务, web 应用, go, docker, 容器"
services: app-service
author: sptramer
manager: cfowler
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: sttramer
ms.custom: mvc
ms.openlocfilehash: dbe4d7bc6f5f1d83a079993c9616206fd82a1b9d
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="deploy-a-go-app-in-azure-web-app-for-containers"></a>在用于容器的 Azure Web 应用中部署 Go 应用

在 Linux 上，应用服务提供预定义的应用程序堆栈，并支持特定版本，例如 PHP 7.0 和 Node.js 4.5。 还可使用自定义 Docker 映像，于尚未在 Azure 中定义的应用程序堆栈中运行 Web 应用。 本快速入门介绍了如何使用现有的带 Go 应用程序的 Docker 容器，让其在 Azure 应用服务上运行。 使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 创建 Web 应用。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-the-container"></a>为容器创建 Web 应用

使用 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 命令在 `myAppServicePlan` 应用服务计划中创建 [Web 应用](../app-service-web-overview.md)。 不要忘记将 `<app name>` 替换为全局唯一的应用名称。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/appservice-go-quickstart
```

在上述命令中，`--deployment-container-image-name` 指向公共 Docker 中心映像 [microsoft/appservice-go-quickstart](https://hub.docker.com/r/microsoft/appservice-go-quickstart)。

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

## <a name="get-data-from-the-apps-endpoint"></a>从应用的终结点获取数据

使用 `curl` 命令联系容器的应用程序提供的 REST API 终结点。

```bash
curl -X GET http://<app_name>.azurewebsites.net:8080/hello
```

```output
Hello world!
```

**祝贺你！** 已将运行 Go 应用程序的自定义 Docker 映像部署到用于容器的 Web 应用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用自定义 Docker 映像](tutorial-custom-docker-image.md)
