---
title: "将 Azure Docker VM 扩展与 Azure CLI 1.0 配合使用 | Microsoft 文档"
description: "了解如何使用 Docker VM 扩展快速安全地在 Azure 中使用资源管理器模板部署 Docker 环境。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: a3cbcf63533f4042dcd695e141655c5814bd7068
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>在 Azure 中通过 Azure CLI 1.0 使用 Docker VM 扩展创建 Docker 环境
Docker 是流行的容器管理和映像处理平台，使用它可在 Linux（和 Windows）上快速操作容器。 在 Azure 中，可以通过各种方法根据需要部署 Docker。 本文重点介绍如何使用 Docker VM 扩展和 Azure 资源管理器模板。 

有关不同部署方法（包括如何使用 Docker Machine 和 Azure 容器服务）的详细信息，请参阅以下文章：

* 若要快速创建应用的原型，可以使用 [Docker Machine](docker-machine.md) 创建一个 Docker 主机。
* 对于更大、更稳定的环境，可以使用 Azure Docker VM 扩展，它还支持使用 [Docker Compose](https://docs.docker.com/compose/overview/) 生成一致的容器部署。 本文详细介绍如何使用 Azure Docker VM 扩展。
* 若要构建提供其他计划和管理工具的生产就绪、可缩放环境，可以[在 Azure 容器服务上部署 Docker Swarm 群集](../../container-service/dcos-swarm/container-service-deployment.md)。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](#azure-docker-vm-extension-overview) – 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0](dockerextension.md) - 适用于资源管理部署模型的下一代 CLI 

## <a name="azure-docker-vm-extension-overview"></a>Azure Docker VM 扩展概述
Azure Docker VM 扩展在 Linux 虚拟机 (VM) 中安装并配置 Docker 守护程序、Docker 客户端和 Docker Compose。 使用 Azure Docker VM 扩展，可以获得更多控制和功能，而不是只是使用 Docker Machine 或自己创建 Docker 主机。 这些附加功能（如 [Docker Compose](https://docs.docker.com/compose/overview/)），使 Azure Docker VM 扩展适用于更可靠的开发人员或生产环境。

Azure 资源管理器模板定义环境的整个结构。 模板可用于创建和配置资源，例如 Docker 主机 VM、存储、基于角色的访问控制 (RBAC) 和诊断功能。 可以重用这些模板，以便以一致的方式创建其他部署。 有关 Azure 资源管理器和模板的详细信息，请参阅[资源管理器概述](../../azure-resource-manager/resource-group-overview.md)。 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>使用 Azure Docker VM 扩展部署模板
让我们使用现有的快速入门模板来创建 Ubuntu VM，以使用 Azure Docker VM 扩展来安装和配置 Docker 主机。 可以在此处查看模板：[使用 Docker 轻松部署 Ubuntu VM](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)。 

需要安装[最新的 Azure CLI](../../cli-install-nodejs.md)，并按如下所示，使用资源管理器模式登录：

```azurecli
azure config mode arm
```

使用 Azure CLI 部署模板，并指定模板 URI。 以下示例在 westus 位置创建名为 myResourceGroup 的资源组。 使用自己的资源组名称和位置，如下所示：

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

根据提示命名存储帐户，提供用户名和密码以及 DNS 名称。 输出类似于以下示例：

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: azureuser
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicidns
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

仅在几秒钟后，Azure CLI 就返回到提示符，但 Azure Docker VM 扩展仍在创建和配置 Docker 主机。 需要几分钟才能完成部署。 可以使用 `azure vm show` 命令查看有关 Docker 主机状态的详细信息。

以下示例在名为 myResourceGroup 的资源组中检查名为 myDockerVM（模板中的默认名称 - 请不要更改该名称）的 VM 的状态。 输入在上一步中创建的资源组的名称：

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

`azure vm show` 命令的输出类似于以下示例：

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicdns.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

在输出的顶部附近，可看到 VM 的 ProvisioningState。 当此项显示“已成功”时，表示部署完毕，此时可通过 SSH 连接到 VM。

在输出快到末尾的位置，FQDN 显示 Docker 主机完全限定的域名。 在剩余步骤中将使用此 FQDN 通过 SSH 连接到 Docker 主机。

## <a name="deploy-your-first-nginx-container"></a>部署第一个 nginx 容器
完成部署后，从本地计算机通过 SSH 登录到新的 Docker 主机。 输入自己的用户名和 FQDN，如下所示：

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com
```

登录到 Docker 主机后，让我们运行 nginx 容器：

```bash
sudo docker run -d -p 80:80 nginx
```

下载 nginx 映像并启动容器时，输出结果类似于以下示例：

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

输出类似于以下示例，它显示 nginx 容器正在运行，TCP 端口 80 和 443 正在转发：

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

若要查看容器的运行情况，请打开 Web 浏览器并输入 Docker 主机的 FQDN 名称：

![运行 ngnix 容器](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Azure Docker VM 扩展模板参考
上一个示例使用现有的快速入门模板。 也可以使用自己的资源管理器模板部署 Azure Docker VM 扩展。 为此，请将以下内容添加到资源管理器模板，并适当地定义 VM 的 vmName：

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
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

有关更详细的资源管理器模板用法演练，请阅读 [Azure Resource Manager overview](../../azure-resource-manager/resource-group-overview.md)（Azure 资源管理器概述）。

## <a name="next-steps"></a>后续步骤
可能需要使用 [Docker Compose](https://docs.docker.com/compose/overview/) [配置 Docker 守护程序 TCP 端口](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket)、了解 [Docker 安全性](https://docs.docker.com/engine/security/security/)或部署容器。 有关 Azure Docker VM 扩展本身的详细信息，请参阅 [GitHub 项目](https://github.com/Azure/azure-docker-extension/)。

阅读有关 Azure 中的其他 Docker 部署选项的详细信息：

* [通过 Azure 驱动程序使用 Docker Machine](docker-machine.md)  
* [开始使用 Docker 和 Compose，在 Azure 虚拟机上定义和运行多容器应用程序](docker-compose-quickstart.md)。
* [部署 Azure 容器服务群集](../../container-service/dcos-swarm/container-service-deployment.md)

