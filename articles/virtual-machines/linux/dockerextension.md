---
title: "使用 Azure Docker VM 扩展 | Microsoft 文档"
description: "了解如何使用 Docker VM 扩展快速安全地在 Azure 中使用 Resource Manager 模板和 Azure CLI 2.0 部署 Docker 环境"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 0cef78edaeec9d45aa733b1912d82d5a058ba289
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>在 Azure 中使用 Docker VM 扩展创建 Docker 环境
Docker 是流行的容器管理和映像处理平台，用于在 Linux 上快速操作容器。 在 Azure 中，可以通过各种方法根据需要部署 Docker。 本文重点介绍如何通过 Azure CLI 2.0 使用 Docker VM 扩展和 Azure Resource Manager 模板。 还可以使用 [Azure CLI 1.0](dockerextension-nodejs.md) 执行这些步骤。

## <a name="azure-docker-vm-extension-overview"></a>Azure Docker VM 扩展概述
Azure Docker VM 扩展在 Linux 虚拟机 (VM) 中安装并配置 Docker 守护程序、Docker 客户端和 Docker Compose。 使用 Azure Docker VM 扩展，可以获得更多控制和功能，而不是只是使用 Docker Machine 或自己创建 Docker 主机。 这些附加功能（如 [Docker Compose](https://docs.docker.com/compose/overview/)），使 Azure Docker VM 扩展适用于更可靠的开发人员或生产环境。

有关不同部署方法（包括如何使用 Docker Machine 和 Azure 容器服务）的详细信息，请参阅以下文章：

* 若要快速创建应用的原型，可以使用 [Docker Machine](docker-machine.md) 创建一个 Docker 主机。
* 若要构建提供其他计划和管理工具的生产就绪、可缩放环境，可以[在 Azure 容器服务上部署 Docker Swarm 群集](../../container-service/dcos-swarm/container-service-deployment.md)。

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>使用 Azure Docker VM 扩展部署模板
让我们使用现有的快速入门模板来创建 Ubuntu VM，以使用 Azure Docker VM 扩展来安装和配置 Docker 主机。 可以在此处查看模板：[使用 Docker 轻松部署 Ubuntu VM](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)。 需要安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并已使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。

首先，使用 [az group create](/cli/azure/group#create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

接下来，使用 [az group deployment create](/cli/azure/group/deployment#create) 部署 VM，其中包含 [GitHub 中此 Azure Resource Manager 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)中的 Azure Docker VM 扩展。 为 newStorageAccountName、adminUsername、adminPassword 和 dnsNameForPublicIP 提供自己的唯一值，如下所示：

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

需要几分钟才能完成部署。 部署完成后，[移到下一步](#deploy-your-first-nginx-container)，将 SSH 移到 VM。 

（可选）要改为将控制返回提示符，并允许部署在后台继续运行，请将 `--no-wait` 标志添加到前一个命令。 此过程允许在 CLI 中执行其他工作，同时部署将持续几分钟。 

可以使用 [az vm show](/cli/azure/vm#show) 查看有关 Docker 主机状态的详细信息。 以下示例在名为 myResourceGroup 的资源组中检查名为 myDockerVM（模板中的默认名称 - 请不要更改该名称）的 VM 的状态：

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --query [provisioningState] \
    --output tsv
```

当此命令返回“已成功”时，表示部署完毕，可使用以下步骤将 SSH 移到 VM。

## <a name="deploy-your-first-nginx-container"></a>部署第一个 NGINX 容器
若要查看 VM 的详细信息（包括 DNS 名称），请使用 [az vm show](/cli/azure/vm#show)：

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

通过 SSH 连接到新的 Docker 主机。 提供自己的 DNS 名称，如下所示：

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

登录到 Docker 主机后，运行 NGINX 容器：

```bash
sudo docker run -d -p 80:80 nginx
```

下载 NGINX 映像并启动容器时，输出结果类似于以下示例：

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

按如下所示检查 Docker 主机上运行的容器的状态：

```bash
sudo docker ps
```

输出类似于以下示例，它显示 NGINX 容器正在运行，且正在转发 TCP 端口 80 和 443：

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

若要查看容器的运行情况，请打开 Web 浏览器并输入 Docker 主机的 DNS 名称：

![运行 ngnix 容器](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Azure Docker VM 扩展模板参考
上一个示例使用现有的快速入门模板。 也可以使用自己的 Resource Manager 模板部署 Azure Docker VM 扩展。 为此，请将以下内容添加到 Resource Manager 模板，并适当地定义 VM 的 `vmName`：

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.*",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

有关更详细的 Resource Manager 模板用法演练，请阅读 [Azure Resource Manager overview](../../azure-resource-manager/resource-group-overview.md)（Azure Resource Manager 概述）。

## <a name="next-steps"></a>后续步骤
可能需要使用 [Docker Compose](https://docs.docker.com/compose/overview/) [配置 Docker 守护程序 TCP 端口](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket)、了解 [Docker 安全性](https://docs.docker.com/engine/security/security/)或部署容器。 有关 Azure Docker VM 扩展本身的详细信息，请参阅 [GitHub 项目](https://github.com/Azure/azure-docker-extension/)。

阅读有关 Azure 中的其他 Docker 部署选项的详细信息：

* [通过 Azure 驱动程序使用 Docker Machine](docker-machine.md)  
* [开始使用 Docker 和 Compose，在 Azure 虚拟机上定义和运行多容器应用程序](docker-compose-quickstart.md)。
* [部署 Azure 容器服务群集](../../container-service/dcos-swarm/container-service-deployment.md)

