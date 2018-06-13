---
title: 快速入门 - 使用 Azure CLI 2.0 创建 Linux VM | Microsoft Docs
description: 本快速入门介绍如何使用 Azure CLI 2.0 创建 Linux 虚拟机
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1c45f8f010d69337d21fce327933990a573988a4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187923"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli-20"></a>快速入门：使用 Azure CLI 2.0 创建 Linux 虚拟机

Azure CLI 2.0 用于从命令行或脚本创建和管理 Azure 资源。 本快速入门演示如何使用 Azure CLI 2.0 在 Azure 中部署运行 Ubuntu 的 Linux 虚拟机 (VM)。 若要查看运行中的 VM，可以通过 SSH 登录到该 VM 并安装 NGINX Web 服务器。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建 VM。

下面的示例创建名为 *myVM* 的 VM，添加名为 *azureuser* 的用户帐户，并生成 SSH 密钥（如果这些密钥尚未存在于默认密钥位置 (*~/.ssh*) 中）。 若要使用一组特定密钥，请使用 `--ssh-key-value` 选项：

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

创建 VM 和支持的资源需要几分钟时间。 以下示例输出显示 VM 创建操作已成功。

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

记下 VM 输出中自己的 `publicIpAddress`。 在后续步骤中，将使用此地址访问 VM。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

默认情况下，在 Azure 中创建 Linux VM 时仅打开 SSH 连接。 使用 [az vm open-port](/cli/azure/vm#az_vm_open_port) 打开 TCP 端口 80 以供 NGINX Web 服务器使用：

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

通过 SSH 照常连接到 VM。 将 publicIpAddress 替换为 VM 的公共 IP 地址（在 VM 的上一输出中记下）：

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>安装 Web 服务器

若要查看运行中的 VM，请安装 NGINX Web 服务器。 若要更新包源并安装最新的 NGINX 包，请从 SSH 会话运行以下命令：

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完成后，`exit` SSH 会话。

## <a name="view-the-web-server-in-action"></a>查看运行中的 Web 服务器

NGINX 已安装，并且现在已从 Internet 打开 VM 上的端口 80 - 可以使用所选的 Web 浏览器查看默认的 NGINX 欢迎页。 使用上一步中获取的 VM 的公共 IP 地址。 以下示例演示了默认 NGINX 网站：

![NGINX 默认站点](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令将其删除。 请确保已退出与 VM 的 SSH 会话，然后删除资源，如下所示：

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，部署了简单的虚拟机，打开了 Web 流量的网络端口，并安装了一个基本 Web 服务器。 若要详细了解 Azure 虚拟机，请继续学习 Linux VM 的教程。


> [!div class="nextstepaction"]
> [Azure Linux 虚拟机教程](./tutorial-manage-vm.md)
