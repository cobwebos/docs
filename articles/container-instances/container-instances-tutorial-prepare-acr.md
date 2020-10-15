---
title: 教程 - 准备容器注册表以部署映像
description: Azure 容器实例教程第 2 部分（共 3 部分）- 准备 Azure 容器注册表并推送映像
ms.topic: tutorial
ms.date: 12/18/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 44a7f21c067897b046413851ef5a2c73bfccc24f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708030"
---
# <a name="tutorial-create-an-azure-container-registry-and-push-a-container-image"></a>教程：创建 Azure 容器注册表并推送容器映像

这是三部分教程的第二部分。 本教程的[第一部分](container-instances-tutorial-prepare-app.md)为 Node.js Web 应用程序创建了 Docker 容器映像。 在本教程中，我们将此映像推送到 Azure 容器注册表。 如果尚未创建容器映像，请返回[教程 1 - 创建容器映像](container-instances-tutorial-prepare-app.md)。

Azure 容器注册表是你在 Azure 中的专用 Docker 注册表。 本教程（该系列的第二部分）的内容包括：

> [!div class="checklist"]
> * 使用 Azure CLI 创建 Azure 容器注册表实例
> * 为 Azure 容器注册表标记容器映像
> * 将映像上传到注册表

下一篇文章（本系列的最后一篇教程）介绍如何将容器从专用注册表部署到 Azure 容器实例。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="tag-container-image"></a>标记容器映像

若要将容器映像推送到 Azure 容器注册表等专用注册表，必须先使用注册表登录服务器的完整名称来标记该映像。

首先，请获取 Azure 容器注册表的完整登录服务器名称。 运行以下 [az acr show][az-acr-show] 命令，并将 `<acrName>` 替换为刚刚创建的注册表名称：

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

例如，如果注册表名为 *mycontainerregistry082*：

```azurecli
az acr show --name mycontainerregistry082 --query loginServer --output table
```

```output
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

使用容器注册表的登录服务器标记 aci-tutorial-app 映像**。 此外，请将 `:v1` 标记添加到映像名称的末尾，指示映像版本号。 将 `<acrLoginServer>` 替换为前面执行的 [az acr show][az-acr-show] 命令的结果。

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

使用专用注册表的完整登录服务器名称标记 aci-tutorial-app 映像后，可以使用 [docker push][docker-push] 命令将该映像推送到注册表**。 将 `<acrLoginServer>` 替换为在前面步骤中获取的完整登录服务器名称。

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

```azurecli
az acr repository list --name mycontainerregistry082 --output table
```

```output
Result
----------------
aci-tutorial-app
```

若要查看特定映像的标记，请使用 [az acr repository show-tags][az-acr-repository-show-tags] 命令。**

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

应该会看到与下面类似的输出：

```console
az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>后续步骤

本教程中，我们准备了要用于 Azure 容器实例的 Azure 容器注册表，并将容器映像推送到了该注册表。 已完成以下步骤：

> [!div class="checklist"]
> * 使用 Azure CLI 创建 Azure 容器注册表实例
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
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
