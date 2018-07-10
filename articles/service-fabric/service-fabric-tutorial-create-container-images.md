---
title: 在 Azure 中的 Service Fabric 上创建容器映像 | Microsoft Docs
description: 本教程介绍如何创建多容器 Service Fabric 应用程序的容器映像。
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, 容器, 微服务, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: a2814ff299d1bfb003b6133e2b75b47a312f8728
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114034"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>教程：在 Linux Service Fabric 群集上创建容器映像

本教程是演示如何在 Linux Service Fabric 群集中使用容器的教程系列的第 1 部分。 本教程中准备了一个要用于 Service Fabric 的多容器应用程序。 在后续教程中，这些映像用作 Service Fabric 应用程序的一部分。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 克隆 GitHub 中的应用程序源
> * 根据应用程序源创建容器映像
> * 部署 Azure 容器注册表 (ACR) 实例
> * 标记 ACR 的容器映像
> * 向 ACR 上传映像

此教程系列介绍了如何：

> [!div class="checklist"]
> * 创建 Service Fabric 的容器映像
> * [使用容器生成和运行 Service Fabric 应用程序](service-fabric-tutorial-package-containers.md)
> * [如何在 Service Fabric 中处理故障转移和缩放](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>先决条件

* 设置 Service Fabric 的 Linux 开发环境。 按照[此处](service-fabric-get-started-linux.md)的说明设置 Linux 环境。
* 本教程需要运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。
* 此外，还需要拥有可用的 Azure 订阅。 有关免费试用版的详细信息，请转到[此处](https://azure.microsoft.com/free/)。

## <a name="get-application-code"></a>获取应用程序代码

本教程使用的示例应用程序是一个投票应用。 该应用程序由前端 Web 组件和后端 Redis 实例组成。 该组件打包到容器映像中。

使用 git 可将应用程序的副本下载到开发环境。

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

该解决方案包含两个文件夹和一个“docker-compose.yml”文件。 “azure-vote”文件夹包含 Python 前端服务以及用于生成映像的 Dockerfile。 “Voting”目录包含部署到群集的 Service Fabric 应用程序包。 这些目录包含本教程所需的资产。

## <a name="create-container-images"></a>创建容器映像

在“azure-vote”目录中，运行以下命令，生成前端 Web 组件的映像。 下面的命令使用此目录中的 Dockerfile 生成映像。

```bash
docker build -t azure-vote-front .
```

此命令可能需要一些时间，因为需要从 Docker 中心拉取所有必需的依赖项。 完成后，使用 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 命令查看创建的映像。

```bash
docker images
```

请注意，已下载或创建两个映像。 *azure-vote-front* 映像包含应用程序。 它派生自 Docker 中心的 python 映像。

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>部署 Azure 容器注册表

首先，运行 **az login** 命令，登录 Azure 帐户。

```bash
az login
```

接下来，使用 **az account** 命令来选择订阅，创建 Azure 容器注册表。 必须输入你的 Azure 订阅的订阅 ID 来代替 <subscription_id>。

```bash
az account set --subscription <subscription_id>
```

在部署 Azure 容器注册表时，首先需要一个资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 **az group create** 命令创建资源组。 在此示例中，在“westus”区域中创建了名为“myResourceGroup”的资源组。

```bash
az group create --name <myResourceGroup> --location westus
```

使用 **az acr create** 命令创建 Azure 容器注册表。 将 \<acrName> 替换为要在订阅下创建的容器注册表的名称。 此名称必须为字母数字且唯一。

```bash
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

在本教程的余下部分，将使用“acrName”作为所选容器注册表名称的占位符。 请记下此值。

## <a name="log-in-to-your-container-registry"></a>登录容器注册表

先登录 ACR 实例，再向其推送映像。 使用 **az acr login** 命令完成此操作。 请提供创建容器注册表时所使用的唯一名称。

```bash
az acr login --name <acrName>
```

完成后，该命令会返回“登录成功”消息。

## <a name="tag-container-images"></a>标记容器映像

需要使用注册表的 loginServer 名称标记每个容器映像。 在将容器映像推送到映像注册表时，使用此标记进行路由。

若要查看当前映像的列表，请使用 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 命令。

```bash
docker images
```

输出：

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

要获取 loginServer 名称，请运行以下命令：

```bash
az acr show --name <acrName> --query loginServer --output table
```

这将输出包含以下结果的表。 在将 **azure-vote-front** 映像推送到下一步中的容器注册表之前，此结果将用于标记该映像。

```bash
Result
------------------
<acrName>.azurecr.io
```

现在，使用容器注册表的 loginServer 标记 azure-vote-front 映像。 另外，将 `:v1` 添加至映像名称的末端。 此标记代表映像版本。

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

标记后即可运行“docker images”验证操作。

输出：

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>将映像推送到注册表

将 azure-vote-front 映像推送到注册表。 

使用以下示例，将 ACR loginServer 名称替换为环境中的 loginServer。

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

docker push 命令需要几分钟才能完成。

## <a name="list-images-in-registry"></a>列出注册表中的映像

若要返回已推送到 Azure 容器注册表的映像列表，请运行 [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list) 命令。 使用 ACR 实例名称更新命令。

```bash
az acr repository list --name <acrName> --output table
```

输出：

```bash
Result
----------------
azure-vote-front
```

完成本教程后，容器映像已存储在专用 Azure 容器注册表实例中。 后续教程会将此映像从 ACR 部署到 Service Fabric 群集。

## <a name="next-steps"></a>后续步骤

本教程中从 Github 拉取了一个应用程序、创建了容器映像并将容器映像推送到注册表。 已完成以下步骤：

> [!div class="checklist"]
> * 克隆 GitHub 中的应用程序源
> * 根据应用程序源创建容器映像
> * 部署 Azure 容器注册表 (ACR) 实例
> * 标记 ACR 的容器映像
> * 向 ACR 上传映像

转到下一教程，了解如何使用 Yeoman 将容器打包到 Service Fabric 应用程序。

> [!div class="nextstepaction"]
> [打包容器并将其部署为 Service Fabric 应用程序](service-fabric-tutorial-package-containers.md)