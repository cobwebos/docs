---
title: 将数据外泄限制为 Azure 存储 - Azure CLI
description: 在本文中，您将了解如何使用 Azure CLI 使用虚拟网络服务终结点策略限制和限制虚拟网络数据向 Azure 存储资源渗透。
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: e01af052a936403162115965f2dc5b3ad46dd9cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271187"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>使用 Azure CLI 使用虚拟网络服务终结点策略管理数据渗入到 Azure 存储帐户

虚拟网络服务终结点策略使您能够通过服务终结点从虚拟网络内对 Azure 存储帐户应用访问控制。 这是保护工作负载、管理允许哪些存储帐户以及允许数据渗漏的位置的关键。
在本文中，学习如何：

* 创建虚拟网络并添加子网。
* 为 Azure 存储启用服务终结点。
* 创建两个 Azure 存储帐户，并允许从上面创建的子网进行网络访问。
* 创建服务终结点策略，仅允许访问其中一个存储帐户。
* 将虚拟机 （VM） 部署到子网。
* 确认从子网对允许的存储帐户的访问。
* 确认从子网对不允许的存储帐户的访问被拒绝。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.28 或更高版本。 要查找版本，请运行 `az --version`。 如果需要安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="create-a-virtual-network"></a>创建虚拟网络

创建虚拟网络之前，必须为虚拟网络创建资源组以及本文中创建的所有其他资源。 使用 [az group create](/cli/azure/group) 创建资源组。 下面的示例在*东部*位置创建名为*myResourceGroup*的资源组。

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet) 创建包含一个子网的虚拟网络。

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>启用服务终结点 

在此示例中，为*Microsoft.存储*创建了一个服务终结点，用于子网*专用*： 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>限制子网的网络访问

使用 [az network nsg create](/cli/azure/network/nsg) 创建网络安全组。 以下示例创建名为 *myNsgPrivate* 的网络安全组。

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet) 将该网络安全组关联到 *Private* 子网。 以下示例将 *myNsgPrivate* 网络安全组关联到 *Private* 子网：

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

使用 [az network nsg rule create](/cli/azure/network/nsg/rule) 创建安全规则。 下面的规则允许对分配给 Azure 存储服务的公共 IP 地址进行出站访问： 

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
```

每个网络安全组包含多个[默认安全规则](security-overview.md#default-security-rules)。 以下规则将替代允许对所有公共 IP 地址进行出站访问的默认安全规则。 `destination-address-prefix "Internet"` 选项拒绝对所有公共 IP 地址进行出站访问。 上一个规则将替代此规则，因为它的优先级更高，上一个规则允许对 Azure 存储的公共 IP 地址进行访问。

```azurecli-interactive
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
```

以下规则允许 SSH 流量从任何位置入站到子网。 该规则将替代拒绝来自 Internet 的所有入站流量的默认安全规则。 允许通过 SSH 访问子网，以便在稍后的步骤中测试连接。

```azurecli-interactive
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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>限制对 Azure 存储帐户的网络访问

本节列出了通过服务终结点限制 Azure 存储帐户从虚拟网络中的给定子网进行的网络访问的步骤。

### <a name="create-a-storage-account"></a>创建存储帐户

创建两个使用[az 存储帐户创建的](/cli/azure/storage/account)Azure 存储帐户。

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

创建存储帐户后，将存储帐户的连接字符串检索到具有[az 存储帐户显示连接字符串的](/cli/azure/storage/account)变量中。 在后面的步骤中将使用此连接字符串来创建文件共享。

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>查看变量的内容并记下在输出中返回的 **AccountKey** 的值，因为后面的步骤中将使用该值。

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>在存储帐户中创建文件共享

使用 [az storage share create](/cli/azure/storage/share) 在存储帐户中创建一个文件共享。 在后面的步骤中，将装载此文件共享来确认对它的网络访问。

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>拒绝对存储帐户的所有网络访问

默认情况下，存储帐户接受来自任何网络中的客户端的网络连接。 若要仅允许所选的网络进行访问，请使用 [az storage account update](/cli/azure/storage/account) 将默认操作更改为 *Deny*。 在拒绝网络访问后，将无法从任何网络访问存储帐户。

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>启用从虚拟网络子网进行网络访问

使用 [az storage account network-rule add](/cli/azure/storage/account/network-rule) 允许从 *Private* 子网对存储帐户进行网络访问。

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>应用策略以允许访问有效的存储帐户

Azure 服务终结点策略仅适用于 Azure 存储。 因此，我们将为 Microsoft 启用服务终结点 *。* 此子网上的存储用于此示例设置。

服务终结点策略应用于服务终结点。 我们将从创建服务终结点策略开始。 然后，我们将在此策略下创建策略定义，以便为此子网将 Azure 存储帐户列入白名单

创建服务终结点策略

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

将允许的存储帐户的资源 URI 保存在变量中。 在执行以下命令之前，*\<请将订阅 id>* 替换为订阅 ID 的实际值。

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

创建&添加策略定义，允许上述 Azure 存储帐户到服务终结点策略

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

并更新虚拟网络子网以与其关联上一步中创建的服务终结点策略

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>验证对 Azure 存储帐户的访问限制

### <a name="create-the-virtual-machine"></a>创建虚拟机

要测试对存储帐户的网络访问，请将 VM 部署到子网。

在*专用*子网中创建具有[az vm 的 VM。](/cli/azure/vm) 如果默认密钥位置中尚不存在 SSH 密钥，该命令会创建它们。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。

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

### <a name="confirm-access-to-storage-account"></a>确认对存储帐户的访问

通过 SSH 登录到 *myVmPrivate* VM。 将*\<公共 Ip 地址>* 替换为*myVmPrivate VM*的公共 IP 地址。

```bash 
ssh <publicIpAddress>
```

为装入点创建一个文件夹：

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

将 Azure 文件共享装载到你创建的目录中。 在执行下面的命令之前，将*\<存储帐户密钥>* 替换为 **$saConnectionString1**中的*帐户密钥*值。

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

你将收到 `user@myVmPrivate:~$` 提示。 Azure 文件共享已成功装载到 */mnt/MyAzureFileShare*。

### <a name="confirm-access-is-denied-to-storage-account"></a>确认已拒绝对存储帐户的访问

从同一 VM *myVmPrivate*中，为装载点创建一个目录：

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

尝试将 Azure 文件共享从*不允许存储帐户*装载到您创建的目录。 本文假定你已部署了 Ubuntu 的最新版本。 如果使用的是 Ubuntu 的早期版本，请参阅[在 Linux 上装载](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来了解有关装载文件共享的其他说明。 

在执行下面的命令之前，请将*\<存储帐户密钥>* 替换为 **$saConnectionString2**中的*AccountKey*值。

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

访问被拒绝，您会收到错误`mount error(13): Permission denied`，因为此存储帐户不在我们应用于子网的服务终结点策略的允许列表中。 

退出与 *myVmPublic* VM 建立的 SSH 会话。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure) 将其删除。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

在本文中，您将服务终结点策略应用于 Azure 存储。" 您创建了 Azure 存储帐户，并且仅从虚拟网络子网对某些存储帐户（因此拒绝其他存储帐户）进行网络访问。 要了解有关服务终结点策略的详细信息，请参阅[服务终结点策略概述](virtual-network-service-endpoint-policies-overview.md)。
