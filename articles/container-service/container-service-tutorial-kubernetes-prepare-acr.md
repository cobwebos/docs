---
title: "Azure 容器服务教程 - 准备 ACR | Microsoft Docs"
description: "Azure 容器服务教程 - 准备 ACR"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: adc00cb52d5805e152ca742f4cd6b9fab3ed3788
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>部署并使用 Azure 容器注册表

Azure 容器注册表 (ACR) 是用于 Docker 容器映像的基于 Azure 的专用注册表。 本教程演示了如何部署 Azure 容器注册表实例并将容器映像推送到其中。 已完成的步骤包括：

> [!div class="checklist"]
> * 部署 Azure 容器注册表实例
> * 标记 ACR 的容器映像
> * 将映像上传到 ACR

在后续教程中，为了安全地运行容器映像，将此 ACR 实例与 Azure 容器服务 Kubernetes 群集集成。 

## <a name="before-you-begin"></a>开始之前

在[上一教程](./container-service-tutorial-kubernetes-prepare-app.md)中，已经为一个简单的 Azure Voting 应用程序创建了容器映像。 在本教程中，这些映像会推送到 Azure 容器注册表。 若尚未创建 Azure Voting 应用映像，请返回[教程 1 – 创建容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。 或者，此处详细说明的步骤适用于任何容器映像。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="deploy-azure-container-registry"></a>部署 Azure 容器注册表

在部署 Azure 容器注册表时，首先需要一个资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create](/cli/azure/group#create) 命令创建资源组。 在此示例中，在“eastus”区域中创建了名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用“az acr create”[](/cli/azure/acr#create)命令创建 Azure 容器注册表。 容器注册表的名称必须唯一。 通过以下示例，使用某些随机字符更新名称。

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic --admin-enabled true
```

## <a name="get-acr-information"></a>获取 ACR 信息 

创建 ACR 实例后，需要名称、登录服务器名称和身份验证密码。 以下代码将返回每个值。 记录所有值，整个教程都会引用这些值。  

ACR 名称和登录服务器：

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrName:name,acrLoginServer:loginServer}" --output table
```

ACR 密码 - 使用 ACR 名称更新。

```azurecli-interactive
az acr credential show --name <acrName> --query passwords[0].value -o tsv
```

## <a name="container-registry-login"></a>容器注册表登录

在将映像推送到 ACR 实例之前必须先登录 ACR 实例。 使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令完成此操作。 运行 docker login 时，需要提供 ACR 登录服务器名称和 ACR 凭据。

```bash
docker login --username=<acrName> --password=<acrPassword> <acrLoginServer>
```

完成后，该命令会返回“登录成功”消息。

## <a name="tag-container-images"></a>标记容器映像

需要使用注册表的 `loginServer` 名称标记每个容器映像。 在将容器映像推送到映像注册表时，使用此标记进行路由。

若要查看当前映像的列表，请使用 `docker images` 命令。

```bash
docker images
```

输出：

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

使用容器注册表的 loginServer 标记 azure-vote-front 映像。 另外，将 `:v1` 添加至映像名称的末端。 此标记代表映像版本号。

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

使用 azure-vote-back 映像重复命令。

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

标记后即可运行 `docker images` 验证操作。

```bash
docker images
```

输出：

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                                      latest              a9dace4e1a17        7 minutes ago       407 MB
mycontainerregistry082.azurecr.io/azure-vote-back    v1                  a9dace4e1a17        7 minutes ago       407 MB
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1                  eaf2b9c57e5e        8 minutes ago       716 MB
mysql                                                latest              e799c7f9ae9c        6 weeks ago         407 MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-acr"></a>将映像推送到 ACR

将 azure-vote-front 映像推送到注册表。 

使用以下示例，将 ACR loginServer 名称替换为环境中的 loginServer。 此操作需要几分钟时间完成。

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

对 azure-vote-back 映像执行相同操作。

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

## <a name="list-images-in-acr"></a>列出 ACR 的映像 

若要返回已推送到 Azure 容器注册表的映像列表，请使用 [az acr repository list](/cli/azure/acr/repository#list) 命令。 使用 ACR 实例名称更新命令。

```azurecli-interactive
az acr repository list --name <acrName> --username <acrName> --password <acrPassword> --output table
```

输出：

```azurecli
Result
----------------
azure-vote-back
azure-vote-front
```

然后，若要查看特定映像的标记，请使用 [az acr repository show-tags](/cli/azure/acr/repository#show-tags) 命令。

```azurecli-interactive
az acr repository show-tags --name <acrName> --username <acrName> --password <acrPassword> --repository azure-vote-front --output table
```

输出：

```azurecli
Result
--------
v1
```

完成教程后，这两个容器映像已存储在专用 Azure 容器注册表实例中。 在后续教程中，这些映像会从 ACR 部署到 Kubernetes 群集。

## <a name="next-steps"></a>后续步骤

在本教程中，准备 Azure 容器注册表，用于 ACS Kubernetes 群集。 已完成以下步骤：

> [!div class="checklist"]
> * 部署 Azure 容器注册表实例
> * 标记 ACR 的容器映像
> * 将映像上传到 ACR

进入下一教程，了解如何在 Azure 中部署 Kubernetes 群集。

> [!div class="nextstepaction"]
> [部署 Kubernetes 群集](./container-service-tutorial-kubernetes-deploy-cluster.md)
