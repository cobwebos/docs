---
title: Azure 快速入门 - 创建 VM CLI | Microsoft 文档
description: 快速了解如何使用 Azure CLI 创建虚拟机。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/13/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a92a72c1b973b10005f058235845555b407eed78
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>使用 Azure CLI 创建 Linux 虚拟机

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本快速入门教程详细介绍了如何使用 Azure CLI 部署运行 Ubuntu 服务器的虚拟机。 服务器部署以后，将创建 SSH 连接，并且安装 NGINX webserver。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建 VM。 

下面的示例创建一个名为 *myVM* 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

```azurecli-interactive 
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

创建 VM 后，Azure CLI 会显示类似于以下示例的信息。 记下 `publicIpAddress`。 此地址用于访问 VM。

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80 

默认情况下，仅允许通过 SSH 连接登录到 Azure 中部署的 Linux 虚拟机。 如果此 VM 将用作 Web 服务器，则需要从 Internet 打开端口 80。 使用 [az vm open-port](/cli/azure/vm#az_vm_open_port) 命令打开所需端口。  
 
 ```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>通过 SSH 连接到 VM

使用以下命令创建与虚拟机的 SSH 会话。 确保将 **publicIpAddress** 替换为虚拟机的相应公共 IP 地址。  在上例中，我们的 IP 地址为 *40.68.254.142*。

```bash 
ssh publicIpAddress
```

## <a name="install-nginx"></a>安装 NGINX

使用以下命令更新包源并安装最新的 NGINX 包。 

```bash 
# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>查看 NGINX 欢迎页

NGINX 已安装，并且现在已从 Internet 打开 VM 上的端口 80 - 可以使用所选的 Web 浏览器查看默认的 NGINX 欢迎页。 请务必使用前面记录的 *publicIpAddress* 访问默认页面。 

![NGINX 默认站点](./media/quick-create-cli/nginx.png) 


## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令将其删除。 退出 SSH 会话，返回 VM，然后删除资源，如下所示：

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，部署了一台简单的虚拟机、一条网络安全组规则，并安装了一台 Web 服务器。 若要详细了解 Azure 虚拟机，请继续学习 Linux VM 的教程。


> [!div class="nextstepaction"]
> [Azure Linux 虚拟机教程](./tutorial-manage-vm.md)
