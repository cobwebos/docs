---
title: Azure 容器实例教程 - 准备 Azure 容器注册表
description: Azure 容器实例教程第 2 部分（共 3 部分）- 准备 Azure 容器注册表
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6e5a6a64e6d7c53bb4ea84de646812c962469d4f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>教程：部署和使用 Azure 容器注册表

这是三部分教程的第二部分。 本教程的[第一部分](container-instances-tutorial-prepare-app.md)为 Node.js Web 应用程序创建了 Docker 容器映像。 在本教程中，我们将此映像推送到 Azure 容器注册表。 如果尚未创建容器映像，请返回[教程 1 - 创建容器映像](container-instances-tutorial-prepare-app.md)。

Azure 容器注册表是你在 Azure 中的专用 Docker 注册表。 本教程在订阅中创建一个 Azure 容器注册表实例，然后将以前创建的容器映像推送到其中。 本文（本系列的第二部分）将介绍如何：

> [!div class="checklist"]
> * 创建 Azure 容器注册表实例
> * 为 Azure 容器注册表标记容器映像
> * 将映像上传到注册表

下一篇文章（本系列的最后一篇教程）介绍如何将容器从专用注册表部署到 Azure 容器实例。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="create-azure-container-registry"></a>创建 Azure 容器注册表

在创建容器注册表之前，需要创建一个资源组，以便将容器注册表部署到其中。 资源组是在其中部署和管理所有 Azure 资源的逻辑集合。

使用 [az group create][az-group-create] 命令创建资源组。 以下示例在 *eastus* 区域创建名为 *myResourceGroup* 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

创建资源组后，使用 [az acr create][az-acr-create] 命令创建 Azure 容器注册表。 容器注册表名称在 Azure 中必须唯一，并且必须包含 5-50 个字母数字字符。 将 `<acrName>` 替换为注册表的唯一名称：

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

下面是名为 *mycontainerregistry082* 的新 Azure 容器注册表的示例输出（此处显示的内容已截断）：

```console
$ az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
...
{
  "adminUserEnabled": true,
  "creationDate": "2018-03-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
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

本教程的余下部分使用 `<acrName>` 作为在此步骤中选择的容器注册表名称的占位符。

## <a name="log-in-to-container-registry"></a>登录到容器注册表

必须先登录到 Azure 容器注册表实例，才能向其推动映像。 使用 [az acr login][az-acr-login] 命令完成此操作。 必须提供创建容器注册表时选择的唯一名称。

```azurecli
az acr login --name <acrName>
```

该命令在完成后返回 `Login Succeeded`：

```console
$ az acr login --name mycontainerregistry082
Login Succeeded
```

## <a name="tag-container-image"></a>标记容器映像

若要将容器映像推送到 Azure 容器注册表等专用注册表，必须先使用注册表登录服务器的完整名称来标记该映像。

首先，请获取 Azure 容器注册表的完整登录服务器名称。 运行以下 [az acr show][az-acr-show] 命令，并将 `<acrName>` 替换为刚刚创建的注册表名称：

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

例如，如果注册表名为 *mycontainerregistry082*：

```console
$ az acr show --name mycontainerregistry082 --query loginServer --output table
Result
------------------------
mycontainerregistry082.azurecr.io
```

现在，使用 [docker images][docker-images] 命令显示本地映像的列表：

```bash
docker images
```

此时应会显示在[前一篇教程](container-instances-tutorial-prepare-app.md)中生成的 *aci-tutorial-app* 映像，以及计算机上包含的其他所有映像：

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

使用容器注册表的 loginServer 标记 aci-tutorial-app 映像。 此外，请将 `:v1` 标记添加到映像名称的末尾，指示映像版本号。 将 `<acrLoginServer>` 替换为前面执行的 [az acr show][az-acr-show] 命令的结果。

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

再次运行 `docker images` 以验证标记操作：

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>向 Azure 容器注册表推送映像

使用专用注册表的完整登录服务器名称标记 *aci-tutorial-app* 映像后，可以使用 [docker push][docker-push] 将其推送到注册表。 将 `<acrLoginServer>` 替换为在前面步骤中获取的完整登录服务器名称。

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

`push` 操作应需要几秒钟到几分钟的时间（具体取决于 Internet 连接），输出应如下所示：

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>列出 Azure 容器注册表中的映像

若要验证刚刚推送的映像是否确实在 Azure 容器注册表中，请使用 [az acr repository list][az-acr-repository-list] 命令列出注册表中的映像。 将 `<acrName>` 替换为容器注册表的名称。

```azurecli
az acr repository list --name <acrName> --output table
```

例如：

```console
$ az acr repository list --name mycontainerregistry082 --output table
Result
----------------
aci-tutorial-app
```

若要查看特定映像的标记，请使用 [az acr repository show-tags][az-acr-repository-show-tags] 命令。

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

应该会看到与下面类似的输出：

```console
$ az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>后续步骤

本教程中，我们准备了要用于 Azure 容器实例的 Azure 容器注册表，并将容器映像推送到了该注册表。 已完成以下步骤：

> [!div class="checklist"]
> * 部署 Azure 容器注册表实例
> * 为 Azure 容器注册表标记容器映像
> * 将映像上传到 Azure 容器注册表

请继续学习下一篇教程，了解如何使用 Azure 容器实例将容器部署到 Azure：

> [!div class="nextstepaction"]
> [将容器部署到 Azure 容器实例](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
