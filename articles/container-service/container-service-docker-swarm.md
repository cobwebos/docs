---
title: "通过 Docker Swarm 进行 Azure 容器服务管理 | Microsoft Docs"
description: "在 Azure 容器服务中将容器部署到 Docker Swarm"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Mesos, Azure"
ms.assetid: af8f6fb2-13dc-429c-b82a-24a741168d42
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 99953be1a9d99cc1fdd5cd46522ccd262c09e25b


---
# <a name="container-management-with-docker-swarm"></a>使用 Docker Swarm 管理容器
Docker Swarm 提供了一个环境，用于跨一组共用的 Docker 主机部署容器化的工作负荷。 Docker Swarm 使用本地 Docker API。 用于在 Docker Swarm 中管理容器的工作流几乎完全与单个容器主机上的工作流相同。 本文档提供了简单的示例来演示如何在 Docker Swarm 的 Azure 容器服务实例中部署容器化的工作负荷。 有关深入介绍 Docker Swarm 的更多文档，请参阅 [Docker.com 上的 Docker Swarm](https://docs.docker.com/swarm/)。

本文档中这些练习的先决条件：

[在 Azure 容器服务中创建 Swarm 群集](container-service-deployment.md)

[连接 Azure 容器服务中的 Swarm 群集](container-service-connect.md)

## <a name="deploy-a-new-container"></a>部署新容器
若要在 Docker Swarm 中创建一个新的容器，请使用 `docker run` 命令（根据上述的先决条件，确保你已打开指向主服务的 SSH 隧道）。 此示例将基于 `yeasy/simple-web` 映像创建一个容器：

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

创建容器后，使用 `docker ps` 返回有关容器的信息。 注意，此处你会发现列出了托管容器的 Swarm 代理：

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

现在，你可以通过 Swarm 代理负载平衡器的公用 DNS 名称访问此容器中运行的应用程序。 可以在 Azure 门户中找到此信息：  

![实际访问结果](media/real-visit.jpg)  

默认情况下，负载平衡器已打开端口 80、8080 和 443。 如果你想要在另一个端口上连接，需要在 Azure 负载平衡器上为代理池打开该端口。

## <a name="deploy-multiple-containers"></a>部署多个容器
由于启动了多个容器，通过多次执行 'docker run'，你可以使用 `docker ps` 命令来查看运行这些容器的主机。 在下面的示例中，三个容器均匀地分布在三个 Swarm 代理上：  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>使用 Docker Compose 部署容器
可以使用 Docker Compose 自动执行多个容器的部署和配置。 为此，请确保已创建安全外壳 (SSH) 隧道并已设置 DOCKER_HOST 变量（请参阅上述的先决条件）。

在本地系统上创建 docker-compose.yml 文件。 为此，请使用此 [示例](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml)。

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

运行 `docker-compose up -d` 以启动容器部署：

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

最后，将返回运行中容器的列表。 此列表反映使用 Docker Compose 部署的容器：

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

当然，也可以使用 `docker-compose ps` 仅检查 `compose.yml` 文件中定义的容器。

## <a name="next-steps"></a>后续步骤
[了解有关 Docker Swarm 的更多信息](https://docs.docker.com/swarm/)




<!--HONumber=Nov16_HO2-->


