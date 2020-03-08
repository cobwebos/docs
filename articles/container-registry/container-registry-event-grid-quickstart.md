---
title: 快速入门-将事件发送到事件网格
description: 在本快速入门中，我们将为容器注册表启用事件网格事件，然后将容器映像推送和删除事件发送到示例应用程序。
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18
ms.openlocfilehash: dbeba56820a520e3435eeb0c5c8dbc5aae981241
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78403243"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>快速入门：将事件从私有容器注册表发送到事件网格

Azure 事件网格是一个完全托管的事件路由服务，可以通过发布-订阅模型提供一致的事件使用数据。 在本快速入门中，我们将使用 Azure CLI 创建容器注册表、订阅注册表事件，然后部署一个示例 Web 应用程序来接收事件。 最后，触发容器映像 `push` 和 `delete` 事件，并在示例应用程序中查看事件负载。

完成本文中的步骤后，从容器注册表发送到事件网格的事件将显示在示例 Web 应用中：

![呈现示例 Web 应用程序的 Web 浏览器，其中包含三个收到的事件][sample-app-01]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][azure-account]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

本文中的 Azure CLI 命令已根据 **Bash** shell 设置了格式。 如果使用其他 shell（例如 PowerShell 或命令提示符），则可能需要相应地调整行连续字符或变量赋值行。 本文使用变量来最大程度地减少所需的命令编辑量。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下[az group create][az-group-create]命令会在*eastus*区域中创建名为*myResourceGroup*的资源组。 若要对资源组使用不同的名称，请将 `RESOURCE_GROUP_NAME` 设置为不同的值。

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>创建容器注册表

接下来，使用以下命令将容器注册表部署到资源组中。 运行[az acr create][az-acr-create]命令之前，请将 `ACR_NAME` 设置为注册表的名称。 该名称在 Azure 中必须唯一，并且限制为 5-50 个字母数字字符。

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

创建注册表之后，Azure CLI 将返回如下所示的输出：

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>创建事件终结点

在本部分，我们使用 GitHub 存储库中的资源管理器模板，将一个预生成的示例 Web 应用程序部署到 Azure 应用服务。 稍后，我们将订阅该注册表的事件网格事件，并将此应用指定为事件要发送到的终结点。

若要部署示例应用，请将 `SITE_NAME` 设置为 Web 应用的唯一名称，并执行以下命令。 站点名称在 Azure 中必须唯一，因为它是 Web 应用的完全限定域名 (FQDN) 的一部分。 在稍后的部分，我们将在 Web 浏览器中导航到应用的 FQDN，以查看注册表的事件。

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

部署成功后（可能需要几分钟时间），打开浏览器并导航到 Web 应用，以确保它正在运行：

`http://<your-site-name>.azurewebsites.net`

此时应会显示不包含任何事件消息的示例应用：

![显示示例 Web 应用的 Web 浏览器，该应用中未显示任何事件][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>订阅注册表事件

在事件网格中订阅一个主题，以告知你要跟踪哪些事件，以及要将事件发送到何处。 以下[az eventgrid event-订阅 create][az-eventgrid-event-subscription-create]命令订阅你创建的容器注册表，并将你的 web 应用的 URL 指定为它应将事件发送到的终结点。 此处可以重复使用在前面几个部分填充的环境变量，因此无需进行编辑。

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

完成订阅后，应会看到如下所示的输出：

```json
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>触发注册表事件

正常运行示例应用并使用事件网格订阅注册表后，可以生成一些事件。 在本部分中，将使用 ACR 任务来生成容器映像并将其推送到注册表。 ACR 任务是 Azure 容器注册表的一项功能，可用于在云中生成容器映像，而无需在本地计算机上安装 Docker 引擎。

### <a name="build-and-push-image"></a>生成和推送映像

执行以下 Azure CLI 命令，基于 GitHub 存储库的内容生成容器映像。 默认情况下，ACR 任务会自动将成功生成的映像推送到注册表，后者会生成 `ImagePushed` 事件。

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

当 ACR 任务生成然后推送映像时，应会显示如下所示的输出。 为简洁起见，以下示例输出已截断。

```output
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

若要验证生成的映像是否在注册表中，请执行以下命令来查看“myimage”存储库中的标记：

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

生成的映像的“v1”标记应显示在输出中，如下所示：

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>删除映像

现在，通过使用[az acr repository delete][az-acr-repository-delete]命令删除图像来生成 `ImageDeleted` 事件：

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

此时应会显示如下所示的输出，其中要求确认删除清单和关联的映像：

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>查看注册表事件

现已将映像推送到注册表，然后已将其删除。 导航到事件网格查看器 Web 应用，应会看到 `ImageDeleted` 和 `ImagePushed` 事件。 此外，还可能会看到在执行[订阅注册表事件](#subscribe-to-registry-events)部分所述命令后生成的订阅验证事件。

以下屏幕截图显示了包含三个事件的示例应用，`ImageDeleted` 事件已展开以显示其详细信息。

![显示示例应用的 Web 浏览器，该应用包含 ImagePushed 和 ImageDeleted 事件][sample-app-03]

祝贺你！ 如果看到 `ImagePushed` 和 `ImageDeleted` 事件，则表示注册表正在将事件发送到事件网格，而事件网格正在将这些事件转发到 Web 应用终结点。

## <a name="clean-up-resources"></a>清理资源

用完本快速入门中创建的资源后，可使用以下 Azure CLI 命令将其全部删除。 删除某个资源组会永久删除其中包含的所有资源。

**警告**：此操作不可逆。 在运行该命令之前，请确认不再需要该组中的任何资源。

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>事件网格事件架构

可以在事件网格文档中找到 Azure 容器注册表事件消息架构参考：

[容器注册表的 Azure 事件网格事件架构](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了一个容器注册表、使用 ACR 任务生成了映像、删除了该映像，并已通过一个示例应用程序从事件网格使用了注册表的事件。 接下来，请继续学习 ACR 任务教程来详细了解如何在云中生成容器映像，包括更新基础映像时自动进行生成：

> [!div class="nextstepaction"]
> [使用 ACR 任务在云中生成容器映像](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
