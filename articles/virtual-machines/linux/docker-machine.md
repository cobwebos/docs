---
title: "使用 Docker Machine 在 Azure 中创建 Linux 主机 | Microsoft Docs"
description: "介绍如何使用 Docker Machine 在 Azure 中创建 Docker 主机。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: iainfou
ms.openlocfilehash: efd0b60079017e476b54acfc65ffe8f35ffe4933
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>如何使用 Docker Machine 在 Azure 中创建主机
本文详细介绍如何使用 [Docker Machine](https://docs.docker.com/machine/) 在 Azure 中创建主机。 `docker-machine` 命令在 Azure 中创建一个 Linux 虚拟机 (VM)，然后安装 Docker。 然后，可以使用相同的本地工具和工作流来管理 Azure 中的 Docker 主机。 若要在 Windows 10 中使用 docker-machine，必须使用 Linux bash。

## <a name="create-vms-with-docker-machine"></a>使用 Docker Machine 创建 VM
首先，使用 [az account show](/cli/azure/account#show) 获取 Azure 订阅 ID，如下所示：

```azurecli
sub=$(az account show --query "id" -o tsv)
```

通过指定 Azure 作为驱动程序，在 Azure 中使用 `docker-machine create` 创建 Docker 主 VM。 有关详细信息，请参阅 [Docker Azure 驱动程序文档](https://docs.docker.com/machine/drivers/azure/)

以下示例基于“标准 D2 v2”计划创建一个名为“myVM”的 VM，创建一个名为“azureuser”的用户帐户，并在主 VM 上打开端口 *80*。 按照任何提示登录你的 Azure 帐户，并授予 Docker Machine 创建和管理资源的权限。

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_D2_v2" \
    myvm
```

输出与以下示例类似：

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvmdocker) Completed machine pre-create checks.
Creating machine...
(myvmdocker) Querying existing resource group.  name="docker-machine"
(myvmdocker) Creating resource group.  name="docker-machine" location="westus"
(myvmdocker) Configuring availability set.  name="docker-machine"
(myvmdocker) Configuring network security group.  name="myvmdocker-firewall" location="westus"
(myvmdocker) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvmdocker) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvmdocker) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvmdocker) Creating public IP address.  name="myvmdocker-ip" static=false
(myvmdocker) Creating network interface.  name="myvmdocker-nic"
(myvmdocker) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvmdocker) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvmdocker"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvmdocker
```

## <a name="configure-your-docker-shell"></a>配置 Docker 外壳
若要连接到 Azure 中的 Docker 主机，请定义适当的连接设置。 如输出的结尾所示，查看 Docker 主机的连接信息，如下所示： 

```bash
docker-machine env myvmdocker
```

输出类似于以下示例：

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvmdocker)
```

若要定义连接设置，可以运行推荐配置命令 (`eval $(docker-machine env myvmdocker)`)，也可以手动设置环境变量。 

## <a name="run-a-container"></a>运行容器
若要查看运行的容器，请运行一个基本的 NGINX Web 服务器。 使用 `docker run` 创建一个容器，并为 Web 流量公开端口 80，如下所示：

```bash
docker run -d -p 80:80 --restart=always nginx
```

输出类似于以下示例：

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

使用 `docker ps` 查看正在运行的容器。 以下示例输出显示公开了端口 80 运行的 NGINX 容器：

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>测试容器
获取 Docker 主机的公共 IP 地址，如下所示：


```bash
docker-machine ip myvmdocker
```

若要查看运行中的容器，请打开 Web 浏览器并输入上述命令输出中注明的公共 IP 地址：

![运行 ngnix 容器](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>后续步骤
也可以使用 [Docker VM 扩展](dockerextension.md)创建主机。 有关使用 Docker Compose 的示例，请参阅 [Azure 中的 Docker 和 Compose 入门](docker-compose-quickstart.md)。
