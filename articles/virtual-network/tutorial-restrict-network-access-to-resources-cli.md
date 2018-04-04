---
title: 限制对 PaaS 资源的网络访问 - Azure CLI | Microsoft Docs
description: 了解如何使用 Azure CLI 通过虚拟网络服务终结点限制对 Azure 资源（例如 Azure 存储和 Azure SQL 数据库）的网络访问。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 5c0c6a802c931b71f5be8b01c610cf0810b0b4d1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>使用 Azure CLI 通过虚拟网络服务终结点限制对 PaaS 资源的网络访问

通过虚拟网络服务终结点，可以将某些 Azure 服务资源限制为仅允许某个虚拟网络子网通过网络进行访问。 还可以删除对资源的 Internet 访问。 服务终结点提供从虚拟网络到受支持 Azure 服务的直接连接，使你能够使用虚拟网络的专用地址空间访问 Azure 服务。 通过服务终结点发往 Azure 资源的流量始终保留在 Microsoft Azure 主干网络上。 在本文中，学习如何：

> [!div class="checklist"]
> * 创建具有一个子网的虚拟网络
> * 添加子网并启用服务终结点
> * 创建 Azure 资源并且仅允许从一个子网对其进行网络访问
> * 将虚拟机 (VM) 部署到每个子网
> * 确认从某个子网对资源的访问
> * 确认已拒绝从某个子网和 Internet 来访问资源

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.28 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="create-a-virtual-network"></a>创建虚拟网络

创建虚拟网络之前，必须为虚拟网络创建资源组以及本文中创建的所有其他资源。 使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建包含一个子网的虚拟网络。

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>启用服务终结点 

只能为支持服务终结点的服务启用服务终结点。 使用 [az network vnet list-endpoint-services](/cli/azure/network/vnet#az_network_vnet_list_endpoint_services) 查看某个 Azure 位置中可用的启用了服务终结点的服务。 以下示例返回 *eastus* 区域中可用的启用了服务终结点的服务列表。 随着更多的 Azure 服务启用服务终结点，返回的服务列表将随时间增大。

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 在虚拟网络中创建另一个子网。 在此示例中，将为子网创建一个用于 *Microsoft.Storage* 的服务终结点： 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-to-and-from-subnet"></a>限制对子网以及来自子网的网络访问

使用 [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) 创建网络安全组。 以下示例创建名为 *myNsgPrivate* 的网络安全组。

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) 将该网络安全组关联到 *Private* 子网。 以下示例将 *myNsgPrivate* 网络安全组关联到 *Private* 子网：

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 创建安全规则。 下面的规则允许对分配给 Azure 存储服务的公用 IP 地址进行出站访问： 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"

Each network security group contains several [default security rules](security-overview.md#default-security-rules). The rule that follows overrides a default security rule that allows outbound access to all public IP addresses. The `destination-address-prefix "Internet"` option denies outbound access to all public IP addresses. The previous rule overrides this rule, due to its higher priority, which allows access to the public IP addresses of Azure Storage.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"

The following rule allows SSH traffic inbound to the subnet from anywhere. The rule overrides a default security rule that denies all inbound traffic from the internet. SSH is allowed to the subnet so that connectivity can be tested in a later step.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>限制对资源的网络访问

对于通过为服务终结点启用的 Azure 服务创建的资源，限制对其的网络访问时所需的步骤因服务而异。 请参阅各个服务的文档来了解适用于每个服务的具体步骤。 作为示例，本文的剩余部分包括了针对 Azure 存储帐户限制网络访问的步骤。

### <a name="create-a-storage-account"></a>创建存储帐户

使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 创建一个 Azure 存储帐户。 将 `<replace-with-your-unique-storage-account-name>` 替换为在所有 Azure 位置中唯一的、长度为 3-24 个字符且仅使用数字和小写字母的名称。

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

创建存储帐户后，使用 [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string) 将存储帐户的连接字符串检索到一个变量中。 在后面的步骤中将使用此连接字符串来创建文件共享。

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>查看变量的内容并记下在输出中返回的 **AccountKey** 的值，因为后面的步骤中将使用该值。

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>在存储帐户中创建文件共享

使用 [az storage share create](/cli/azure/storage/share#az_storage_share_create) 在存储帐户中创建一个文件共享。 在后面的步骤中，将装载此文件共享来确认对它的网络访问。

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>拒绝对存储帐户的所有网络访问

默认情况下，存储帐户接受来自任何网络中的客户端的网络连接。 若要仅允许所选的网络进行访问，请使用 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 将默认操作更改为 *Deny*。 在拒绝网络访问后，将无法从任何网络访问存储帐户。

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>启用从子网的网络访问

使用 [az storage account network-rule add](/cli/azure/storage/account/network-rule#az_storage_account_network_rule_add) 允许从 *Private* 子网对存储帐户进行网络访问。

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>创建虚拟机

若要测试对存储帐户的网络访问，请向每个子网部署 VM。

### <a name="create-the-first-virtual-machine"></a>创建第一台虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 在公共子网中创建一个 VM。 如果默认密钥位置中尚不存在 SSH 密钥，该命令会创建它们。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

创建 VM 需要几分钟时间。 创建 VM 之后，Azure CLI 将显示类似于以下示例的信息： 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

记下返回的输出中的 **publicIpAddress**。 在后面的步骤中会使用此地址通过 Internet 访问 VM。

### <a name="create-the-second-virtual-machine"></a>创建第二台虚拟机

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

创建 VM 需要几分钟时间。 在创建后，记下返回的输出中的 **publicIpAddress**。 在后面的步骤中会使用此地址通过 Internet 访问 VM。

## <a name="confirm-access-to-storage-account"></a>确认对存储帐户的访问

通过 SSH 登录到 *myVmPrivate* VM。 将 *<publicIpAddress>* 替换为 *myVmPrivate* VM 的公用 IP 地址。

```bash 
ssh <publicIpAddress>
```

为装入点创建一个文件夹：

```bash
sudo mkdir /mnt/MyAzureFileShare
```

将 Azure 文件共享装载到你创建的目录中。 在运行以下命令之前，将 `<storage-account-name>` 替换为在[创建存储帐户](#create-a-storage-account)中检索到的帐户名称，将 `<storage-account-key>` 替换为检索到的密钥。

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

你将收到 `user@myVmPrivate:~$` 提示。 Azure 文件共享已成功装载到 */mnt/MyAzureFileShare*。

确认 VM 没有到任何其他公用 IP 地址的出站连接：

```bash
ping bing.com -c 4
```

你不会收到回复，因为除了分配给 Azure 存储服务的地址之外，关联到 *Private* 子网的网络安全组不允许对其他公用 IP 地址的出站访问。

退出与 *myVmPrivate* VM 建立的 SSH 会话。

## <a name="confirm-access-is-denied-to-storage-account"></a>确认已拒绝对存储帐户的访问

使用以下命令来与 *myVmPublic* VM 建立 SSH 会话。 将 `<publicIpAddress>` 替换为 *myVmPublic* VM 的公用 IP 地址： 

```bash 
ssh <publicIpAddress>
```

为装入点创建一个目录：

```bash
sudo mkdir /mnt/MyAzureFileShare
```

尝试将 Azure 文件共享装载到你创建的目录中。 本教程假定你已部署了 Ubuntu 的最新版本。 如果使用的是 Ubuntu 的早期版本，请参阅[在 Linux 上装载](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来了解有关装载文件共享的其他说明。 在运行以下命令之前，将 `<storage-account-name>` 替换为在[创建存储帐户](#create-a-storage-account)中检索到的帐户名称，将 `<storage-account-key>` 替换为检索到的密钥：

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

访问被拒绝，并且你将收到一个 `mount error(13): Permission denied` 错误，因为 *myVmPublic* VM 部署在 *Public* 子网内。 *Public* 子网没有为 Azure 存储启用服务终结点，并且存储帐户仅允许来自 *Private* 子网的网络访问，不允许来自 *Public* 子网的网络访问。

退出与 *myVmPublic* VM 建立的 SSH 会话。

从计算机中，尝试使用 [az storage share list](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) 查看存储帐户中的共享。 将 `<account-name>` 和 `<account-key>` 替换为在[创建存储帐户](#create-a-storage-account)中获得的存储帐户名称和密钥：

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

访问被拒绝，你将收到“此请求无权执行此操作” 错误，因为你的计算机不在 *MyVirtualNetwork* 虚拟网络的 *Private* 子网中。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure#az_group_delete) 将其删除。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

在本教程中，你为虚拟网络子网启用了服务终结点。 你已了解可以为通过多个 Azure 服务部署的资源启用服务终结点。 你创建了一个 Azure 存储帐户并将该存储帐户限制为仅可供某个虚拟网络子网中的资源进行网络访问。 在生产虚拟网络中创建服务终结点之前，建议你充分熟悉[服务终结点](virtual-network-service-endpoints-overview.md)。

如果你的帐户中有多个虚拟网络，你可能希望将两个虚拟网络连接到一起，以便每个虚拟网络中的资源可以彼此进行通信。 请前进到下一教程来了解如何连接虚拟网络。

> [!div class="nextstepaction"]
> [连接虚拟网络](./tutorial-connect-virtual-networks-cli.md)
