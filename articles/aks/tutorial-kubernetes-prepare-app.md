---
title: "Azure 上的 Kubernetes 教程 - 准备应用"
description: "AKS 教程 - 准备应用"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 60e0feb1e45ac5d9f35eac9667eaf9004d77e86a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="prepare-application-for-azure-container-service-aks"></a>准备用于 Azure 容器服务 (AKS) 的应用程序

在本教程的第 1 部分（共 8 部分）中，将准备一个要在 Kubernetes 中使用的多容器应用程序。 已完成的步骤包括：  

> [!div class="checklist"]
> * 克隆 GitHub 中的应用程序源  
> * 根据应用程序源创建容器映像
> * 在本地 Docker 环境中测试应用程序

完成后，可在本地开发环境中访问以下应用程序。

![Azure 上的 Kubernetes 群集映像](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

在后续教程中，此容器映像会上传到 Azure 容器注册表，然后在 AKS 群集中运行。

## <a name="before-you-begin"></a>开始之前

本教程假定基本了解核心 Docker 的概念，如容器、容器映像和基本的 Docker 命令。 如有需要，请参阅 [Docker 入门][docker-get-started]，了解容器基本知识。 

若要完成本教程，需要 Docker 开发环境。 Docker 提供的包可在任何 [Mac][docker-for-mac]、[Windows][docker-for-windows] 或 [Linux][docker-for-linux] 系统上轻松配置 Docker。

Azure Cloud Shell 不包含完成本教程每个步骤所需的 Docker 组件。 因此，我们建议使用完整的 Docker 开发环境。

## <a name="get-application-code"></a>获取应用程序代码

本教程使用的示例应用程序是一个基本的投票应用。 该应用程序由前端 Web 组件和后端 Redis 实例组成。 Web 组件打包到自定义容器映像中。 Redis 实例使用 Docker 中心提供的未修改的映像。  

使用 git 可将应用程序的副本下载到开发环境。

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

将目录更改为克隆的目录，以供使用。

```console
cd azure-voting-app-redis
```

目录内包含应用程序源代码、预创建的 Docker Compose 文件和 Kubernetes 清单文件。 整套教程都会使用这些文件。 

## <a name="create-container-images"></a>创建容器映像

使用 [Docker Compose][docker-compose]，可自动根据容器映像生成并部署多容器应用程序。

运行 `docker-compose.yaml` 文件以创建容器映像，下载 Redis 映像，并启动应用程序。

```console
docker-compose up -d
```

完成后，使用 [docker images][docker-images] 命令查看创建的映像。

```console
docker images
```

请注意，已下载或创建三个映像。 `azure-vote-front` 映像包含应用程序，并以 `nginx-flask` 映像为依据。 `redis` 映像用于启动 Redis 实例。

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

运行 [docker ps][docker-ps] 命令，查看正在运行的容器。

```console
docker ps
```

输出：

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>在本地测试应用程序

浏览到 http://localhost:8080 查看正在运行的应用程序。

![Azure 上的 Kubernetes 群集映像](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>清理资源

现已验证应用程序功能，可停止并删除正在运行的容器。 请勿删除容器映像。 在下一教程中，需要将 `azure-vote-front` 映像上传到 Azure 容器注册表实例中。

运行以下命令，停止正在运行的容器。

```console
docker-compose stop
```

运行以下命令，删除已停止的容器和资源。

```console
docker-compose down
```

完成后，便拥有包含 Azure Vote 应用程序的容器映像。

## <a name="next-steps"></a>后续步骤

本教程测试了应用程序并针对应用程序创建了容器映像。 已完成以下步骤：

> [!div class="checklist"]
> * 克隆 GitHub 中的应用程序源  
> * 根据应用程序源创建容器映像
> * 在本地 Docker 环境中测试应用程序

请转到下一教程，了解如何在 Azure 容器注册表中存储容器映像。

> [!div class="nextstepaction"]
> [向 Azure 容器注册表推送映像][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md