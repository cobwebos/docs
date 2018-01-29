---
title: "Azure 容器实例教程 - 准备 Azure 容器注册表"
description: "Azure 容器实例教程第 2 部分（共 3 部分）- 准备 Azure 容器注册表"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 94ecba44b8281460da4518c146aab814d2eaa850
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>部署并使用 Azure 容器注册表

这是三部分教程的第二部分。 在[上一步](container-instances-tutorial-prepare-app.md)中，介绍了如何为采用 [Node.js][nodejs] 编写的简单 Web 应用程序创建容器映像。 在本教程中，会将此映像推送到 Azure 容器注册表。 若尚未创建容器映像，请返回[教程 1 - 创建容器映像](container-instances-tutorial-prepare-app.md)。

Azure 容器注册表是用于 Docker 容器映像的基于 Azure 的专用注册表。 本教程演示了如何部署 Azure 容器注册表实例，并向其推送容器映像。

本文（本系列的第二部分）将介绍如何：

> [!div class="checklist"]
> * 部署 Azure 容器注册表实例
> * 为 Azure 容器注册表标记容器映像
> * 将映像上传到注册表

在下一篇文章（本系列的最后一篇教程）中，将介绍如何将容器从专用注册表部署到 Azure 容器实例。

## <a name="before-you-begin"></a>开始之前

本教程要求运行 Azure CLI 2.0.23 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0][azure-cli-install]。

若要完成本教程，需要本地安装的 Docker 开发环境。 Docker 提供的包可在任何 [Mac][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。

Azure Cloud Shell 不包含完成本教程每个步骤所需的 Docker 组件。 若要完成本教程，必须在本地计算机安装 Azure CLI 和 Docker 开发环境。

## <a name="deploy-azure-container-registry"></a>部署 Azure 容器注册表

在部署 Azure 容器注册表时，首先需要一个资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用 [az group create][az-group-create] 命令创建资源组。 在此示例中，在“eastus”区域中创建了名为“myResourceGroup”的资源组。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az acr create][az-acr-create] 命令创建 Azure 容器注册表。 容器注册表名称在 Azure 中必须唯一，并且必须包含 5-50 个字母数字字符。 将 `<acrName>` 替换为注册表的唯一名称：

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

以创建名为 mycontainerregistry082 的 Azure 容器注册表为例：

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

在本教程的其余部分，使用 `<acrName>` 作为所选容器注册表名称的占位符。

## <a name="container-registry-login"></a>容器注册表登录

必须先登录到 Azure 容器注册表实例，才能向其推动映像。 使用 [az acr login][az-acr-login] 命令完成此操作。 必须提供创建容器注册表时所提供的唯一名称。

```azurecli
az acr login --name <acrName>
```

该命令在完成后会返回消息 `Login Succeeded`。

## <a name="tag-container-image"></a>标记容器映像

若要从专用注册表部署容器映像，需使用注册表的 `loginServer` 名称标记该映像。

若要查看当前映像的列表，请使用 [docker images][docker-images] 命令。

```bash
docker images
```

输出：

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

若要获取 loginServer 名称，请运行 [az acr show][az-acr-show] 命令。 将 `<acrName>` 替换为容器注册表的名称。

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

示例输出：

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

使用容器注册表的 loginServer 标记 aci-tutorial-app 映像。 另外，将 `:v1` 添加至映像名称的末端。 此标记代表映像版本号。 将 `<acrLoginServer>` 替换为刚才执行的 [az acr show][az-acr-show] 命令的结果。

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

标记后即可运行 `docker images` 验证操作。

```bash
docker images
```

输出：

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>向 Azure 容器注册表推送映像

使用 [docker push][docker-push] 命令将 aci-tutorial-app 映像推送到注册表。 将 `<acrLoginServer>` 替换为在先前步骤中获得的完整登录服务器名称。

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

`push` 操作应需要几秒钟到几分钟的时间（具体取决于 Internet 连接），输出应如下所示：

```bash
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

若要返回已推送到 Azure 容器注册表的映像列表，请使用 [az acr repository list][az-acr-repository-list] 命令。 使用容器注册表名称更新命令。

```azurecli
az acr repository list --name <acrName> --output table
```

输出：

```azurecli
Result
----------------
aci-tutorial-app
```

然后，若要查看特定映像的标记，请使用 [az acr repository show-tags][az-acr-repository-show-tags] 命令。

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

输出：

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>后续步骤

本教程中，准备了要与 Azure 容器实例配合使用的 Azure 容器注册表，并将容器映像推送到注册表。 已完成以下步骤：

> [!div class="checklist"]
> * 部署 Azure 容器注册表实例
> * 为 Azure 容器注册表标记容器映像
> * 将映像上传到 Azure 容器注册表

转到下一教程，了解如何使用 Azure 容器实例将容器部署到 Azure。

> [!div class="nextstepaction"]
> [将容器部署到 Azure 容器实例](./container-instances-tutorial-deploy-app.md)

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
