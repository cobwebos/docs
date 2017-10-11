---
title: "使用 Docker Machine 在 Azure 中创建 Docker 主机 | Microsoft Docs"
description: "介绍如何使用 Docker Machine 在 Azure 中创建 Docker 主机。"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 766d327a87ed13e04166d71c3d9ae0a1e7a66d19
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>使用 Docker-Machine 在 Azure 中创建 Docker 主机
运行 [Docker](https://www.docker.com/) 容器时需要使用运行 Docker 守护程序的主机 VM。
本主题介绍如何使用 [docker-machine](https://docs.docker.com/machine/) 命令创建在 Azure 中运行的配置有 Docker 守护程序的新 Linux VM。 

**注意：** 

* *本文以 docker-machine 0.9.0-rc2 版或更高版本为基础*
* *在不久的未来，将通过 docker-machine 支持 Windows 容器*

## <a name="create-vms-with-docker-machine"></a>使用 Docker Machine 创建 VM
通过 `azure` 驱动程序，使用 `docker-machine create` 命令在 Azure 中创建 Docker 主机 VM。 

Azure 驱动程序需要使用订阅 ID。 可以使用 [Azure CLI](cli-install-nodejs.md) 或 [Azure 门户](https://portal.azure.com)来检索 Azure 订阅。 

**使用 Azure 门户**

* 从左侧的导航页面中选择“订阅”，然后复制订阅 ID。

**使用 Azure CLI**

* 键入 ```azure account list``` 并复制订阅 ID。

键入 `docker-machine create --driver azure` 可查看选项及其默认值。
也可以查看 [Docker Azure 驱动程序文档](https://docs.docker.com/machine/drivers/azure/)来了解详细信息。 

以下示例依赖于[默认值](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22)，但会根据需要设置以下值： 

* azure-dns 表示与生成的公共 IP 和证书关联的名称。 这是虚拟机的 DNS 名称。 VM 可以安全地停止、释放动态 IP，并且能够在 VM 使用新 IP 重新启动后再次连接。 对于区域 UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com，名称前缀必须唯一。
* 打开 VM 上的用于出站 Internet 访问的端口 80
* 用于利用更快的高级存储的 VM 的大小
* 用于 vm 磁盘的高级存储

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>使用 docker-machine 选择 Docker 主机
docker-machine 中有主机的条目之后，即可在运行 docker 命令时设置默认主机。

## <a name="using-powershell"></a>使用 PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>使用 Bash
```bash
eval $(docker-machine env MyDockerHost)
```

现在可以对指定的主机运行 docker 命令

```
docker ps
docker info
```

## <a name="run-a-container"></a>运行容器
配置主机之后，现在可以运行简单的 Web 服务器，来测试是否已正确配置主机。
此处使用了标准的 nginx 映像，指定它应侦听端口 80，并且如果主机 VM 重新启动，容器也将重新启动 (`--restart=always`)。 

```bash
docker run -d -p 80:80 --restart=always nginx
```

输出应如下所示：

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>测试容器
使用 `docker ps` 检查正在运行的容器：

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

若要查看正在运行的容器，请键入 `docker-machine ip <VM name>` 查找要在浏览器中输入的 IP 地址：

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![运行 ngnix 容器](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>摘要
使用 docker-machine，可以在 Azure 中轻松预配 Docker 主机来进行个别 Docker 主机验证。
有关容器的生产托管，请参阅 [Azure 容器服务](http://aka.ms/AzureContainerService)

若要使用 Visual Studio 开发 .NET Core 应用程序，请参阅 [Docker Tools for Visual Studio](http://aka.ms/DockerToolsForVS)

