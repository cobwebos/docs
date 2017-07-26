---
title: "Azure 容器服务教程 - 准备应用 | Microsoft Docs"
description: "Azure 容器服务教程 - 准备应用"
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
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 49d727de69c700af0ae6402ad54b5880010ebb3b
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---

# <a name="create-container-images-to-be-used-with-azure-container-service"></a>创建要与 Azure 容器服务一起使用的容器映像

本教程为 Kubernetes 准备应用程序。 已完成的步骤包括：  

> [!div class="checklist"]
> * 克隆 GitHub 中的应用程序源  
> * 根据应用程序源创建容器映像
> * 在本地 Docker 环境中测试映像

后续教程中，这些容器映像将被上传到 Azure 容器注册表中，然后在 Azure 托管的 Kubernetes 群集中运行。

## <a name="before-you-begin"></a>开始之前

本教程假定基本了解核心 Docker 的概念，如容器、容器映像和基本的 Docker 命令。 如需要，请参阅 [Docker 入门]( https://docs.docker.com/get-started/)，了解容器基本知识。 

若要完成本教程，需要 Docker 开发环境。 Docker 提供的包可在任何 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系统上轻松配置 Docker。

## <a name="get-application-code"></a>获取应用程序代码

本教程使用的示例应用程序是一个基本的投票应用。 该应用程序由前端 Web 组件和后端数据库组成。 

使用 git 可将应用程序的副本下载到开发环境。

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

在应用程序目录中，可以找到预先创建的 Dockerfile 和 Kubernetes 清单文件。 整套教程都使用这些文件创建资产。 

## <a name="create-container-images"></a>创建容器映像

若要为应用程序前端创建容器映像，请使用 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令。

```bash
docker build ./azure-voting-app/azure-vote -t azure-vote-front
```

重复该命令，这次是为了创建后端容器映像。

```bash
docker build ./azure-voting-app/azure-vote-mysql -t azure-vote-back
```

完成后，使用 `docker images` 命令可查看创建的映像。 

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

## <a name="test-application"></a>测试应用程序

现已创建了两个容器映像，请在本地开发环境中测试这些映像。 

首先，创建 Docker 网络。 此网络用于容器之间的通信。  

```bash
docker network create azure-vote
```

使用 `docker run` 命令运行后端容器映像的实例。

在此示例中，mysql 数据库文件存储在容器内。 此应用程序移动到 Kubernete 群集后，使用外部数据卷存储数据库文件。 此外，也可使用环境变量设置 MySQL 凭据。

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

运行前端容器映像的实例。

使用环境变量配置数据库连接信息。

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front
```

完成后，运行 `docker ps` 可查看正在运行的容器。  

```bash
docker ps
```

输出：

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
3aa02e8ae965        azure-vote-front     "/usr/bin/supervisord"   59 seconds ago      Up 57 seconds       443/tcp, 0.0.0.0:8080->80/tcp   flaskmysqlvote_azure-vote-front_1
5ae60b3ba181        azure-vote-backend   "docker-entrypoint..."   59 seconds ago      Up 58 seconds       0.0.0.0:3306->3306/tcp          azure-vote-back
```

浏览到 `http://localhost:8080`查看正在运行的应用程序。 应用程序初始化需要几秒钟。 如果遇到错误，请重试。

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-tutorials/vote-app.png)

## <a name="clean-up-resources"></a>清理资源

现已验证应用程序功能，可停止并删除正在运行的容器。 请勿删除容器映像。 在下一教程中，这些映像将上传到 Azure 容器注册表实例中。

使用 [docker rm](https://docs.docker.com/engine/reference/commandline/rm/) 命令停止并删除前端容器。 

```bash
docker rm -f azure-vote-front
```

使用 [docker rm](https://docs.docker.com/engine/reference/commandline/rm/) 命令停止并删除后端容器。 

```bash
docker rm -f azure-vote-back
```

使用 [docker network rm](https://docs.docker.com/engine/reference/commandline/network_rm/) 命令删除网络。

```bash
docker network rm azure-vote
```

完成后，便拥有组成 Azure 投票应用程序的两个容器印象。

## <a name="next-steps"></a>后续步骤

本教程测试了应用程序并针对应用程序创建了容器映像。 已完成以下步骤：

> [!div class="checklist"]
> * 克隆 GitHub 中的应用程序源  
> * 根据应用程序源创建容器映像
> * 在本地 Docker 环境中测试映像

请转到下一教程，了解如何在 Azure 容器注册表中存储容器映像。

> [!div class="nextstepaction"]
> [向 Azure 容器注册表推送映像](./container-service-tutorial-kubernetes-prepare-acr.md)
