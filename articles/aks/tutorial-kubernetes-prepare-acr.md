---
title: "Azure 上的 Kubernetes 教程 - 准备 ACR"
description: "AKS 教程 - 准备 ACR"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b676898cca7e5c80d6ba872e76a9effdb852bfff
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>部署并使用 Azure 容器注册表

Azure 容器注册表 (ACR) 是用于 Docker 容器映像的基于 Azure 的专用注册表。 本教程的第 2 部分（共 8 部分）演示了如何部署 Azure 容器注册表实例并向其推送容器映像。 已完成的步骤包括：

> [!div class="checklist"]
> * 部署 Azure 容器注册表 (ACR) 实例
> * 标记 ACR 的容器映像
> * 将映像上传到 ACR

在后续教程中，此 ACR 实例将与 AKS 中的 Kubernetes 群集集成。

## <a name="before-you-begin"></a>开始之前

在[上一教程][aks-tutorial-prepare-app]中，已经为一个 Azure Voting 应用程序示例创建了容器映像。 如果尚未创建 Azure Voting 应用映像，请返回到[教程 1：创建容器映像][aks-tutorial-prepare-app]。

本教程要求运行 Azure CLI 2.0.27 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="deploy-azure-container-registry"></a>部署 Azure 容器注册表

在部署 Azure 容器注册表时，首先需要一个资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create][az-group-create] 命令创建资源组。 在此示例中，在 `eastus` 区域中创建了名为 `myResourceGroup` 的资源组。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az acr create][az-acr-create] 命令创建 Azure 容器注册表。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

在本教程的剩余部分中，使用 `<acrName>` 作为容器注册表名称的占位符。

## <a name="container-registry-login"></a>容器注册表登录

运行 [az acr login][az-acr-login] 命令，登录 ACR 实例。 需要提供创建容器注册表时所使用的唯一名称。

```azurecli
az acr login --name <acrName>
```

完成后，该命令会返回“登录成功”消息。

## <a name="tag-container-images"></a>标记容器映像

若要查看当前映像的列表，请使用 [docker images][docker-images] 命令。

```console
docker images
```

输出：

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

需要使用注册表的 loginServer 名称标记每个容器映像。 在将容器映像推送到映像注册表时，使用此标记进行路由。

若要获取 loginServer 名称，请运行以下命令。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

此时，使用容器注册表的 loginServer 标记 `azure-vote-front` 映像。 另外，将 `:v1` 添加至映像名称的末端。 此标记代表映像版本。

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

标记后即可运行 [docker images][docker-images] 验证操作。

```console
docker images
```

输出：

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>将映像推送到注册表

将 `azure-vote-front` 映像推送到注册表。

使用以下示例，将 ACR loginServer 名称替换为环境中的 loginServer。

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

此操作需要几分钟时间完成。

## <a name="list-images-in-registry"></a>列出注册表中的映像

若要返回已推送到 Azure 容器注册表的映像列表，请使用 [az acr repository list][az-acr-repository-list] 命令。 使用 ACR 实例名称更新命令。

```azurecli
az acr repository list --name <acrName> --output table
```

输出：

```azurecli
Result
----------------
azure-vote-front
```

然后，若要查看特定映像的标记，请使用 [az acr repository show-tags][az-acr-repository-show-tags] 命令。

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

输出：

```azurecli
Result
--------
v1
```

完成本教程后，容器映像已存储在专用 Azure 容器注册表实例中。 在后续教程中，此映像会从 ACR 部署到 Kubernetes 群集。

## <a name="next-steps"></a>后续步骤

在本教程中，准备了要用于 AKS 群集的 Azure 容器注册表。 已完成以下步骤：

> [!div class="checklist"]
> * 部署 Azure 容器注册表实例
> * 标记 ACR 的容器映像
> * 将映像上传到 ACR

进入下一教程，了解如何在 Azure 中部署 Kubernetes 群集。

> [!div class="nextstepaction"]
> [部署 Kubernetes 群集][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md