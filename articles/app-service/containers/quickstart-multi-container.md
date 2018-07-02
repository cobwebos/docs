---
title: 使用 Docker Compose 配置在用于容器的 Azure Web 应用中创建多容器（预览版）应用
description: 在数分钟内在用于容器的 Azure Web 应用中部署你的第一个多容器应用
keywords: azure 应用服务, web 应用, linux, docker, compose, 多容器, 容器, kubernetes
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: ec5c92415668c925fe360c0c8887fd792a121842
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753705"
---
# <a name="create-a-multicontainer-preview-app-using-web-app-for-containers"></a>使用用于容器的 Web 应用创建多容器（预览版）应用

在[用于容器的 Web 应用](app-service-linux-intro.md)中可以灵活使用 Docker 映像。 本快速入门展示了如何在 [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) 中使用 Docker Compose 配置将多容器应用部署到用于容器的 Web 应用。 对于 Kubernetes，请遵循[多容器教程](tutorial-multi-container-app.md)中的 Kubernetes 步骤。

你将在 Cloud Shell 中完成本快速入门，但是也可以使用 [Azure CLI](/cli/azure/install-azure-cli)（2.0.32 或更高版本）在本地运行这些命令。 本快速入门将使用一个 Docker Compose 配置文件。

![用于容器的 Web 应用中的示例多容器应用][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>下载示例

本快速入门使用 [Docker](https://docs.docker.com/compose/wordpress/#define-the-project) 中的 compose 文件，但我们将对其进行修改，以包含 Azure Database for MySQL、持久性存储和 Redis。 可在 [Azure 示例](https://github.com/Azure-Samples/multicontainerwordpress)中找到该配置文件。

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

在 Cloud Shell 中，创建一个 quickstart 目录，然后切换到该目录。

```bash
mkdir quickstart

cd quickstart
```

接下来，运行以下命令将示例应用存储库克隆到 quickstart 目录。

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress
```

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

在 Cloud Shell 中，使用 [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) 命令创建资源组。 以下示例在“美国中南部”位置创建名为 *myResourceGroup* 的资源组。 若要查看**标准**层中 Linux 上的应用服务支持的所有位置，请运行 [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations) 命令。

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

通常在附近的区域中创建资源组和资源。

此命令完成后，JSON 输出会显示资源组属性。

## <a name="create-an-azure-app-service-plan"></a>创建 Azure 应用服务计划

在 Cloud Shell 中，使用 [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) 命令在资源组中创建应用服务计划。

以下示例在**标准**定价层 (`--sku S1`) 和 Linux 容器 (`--is-linux`) 中创建名为 `myAppServicePlan` 的应用服务计划。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

创建应用服务计划后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>创建 Docker Compose 应用

在 Cloud Shell 终端 中，切换到 `multicontainerwordpress` 目录。 使用 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 命令在 `myAppServicePlan` 应用服务计划中创建一个多容器 [Web 应用](app-service-linux-intro.md)。 不要忘记将 _\<app_name>_ 替换为唯一的应用名称。

```bash
cd multicontainerwordpress

az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

```json
{
  "additionalProperties": {},
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

### <a name="browse-to-the-app"></a>浏览到应用

浏览到所部署的应用 (`http://<app_name>.azurewebsites.net`)。 该应用可能需要几分钟时间才能完成加载。 如果收到错误，请在几分钟后刷新浏览器。

![用于容器的 Web 应用中的示例多容器应用][1]

**恭喜**，你已在用于容器的 Web 应用中创建了多容器应用。

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在用于容器的 Web 应用中创建多容器 WordPress 应用](tutorial-multi-container-app.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png