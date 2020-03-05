---
title: 快速入门：创建 NAT 网关 - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: 本快速入门介绍如何使用 Azure CLI 创建 NAT 网关
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 1317ea6526a28c5d71dce1a3c25b2fd05f5e446b
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271306"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>快速入门：使用 Azure CLI 创建 NAT 网关

本快速入门介绍如何使用 Azure 虚拟网络 NAT 服务。 你将创建一个 NAT 网关，以便为 Azure 中的虚拟机提供出站连接。 

>[!NOTE] 
>Azure 虚拟网络 NAT 目前以公共预览版提供，仅在有限的几个[区域](https://azure.microsoft.com/global-infrastructure/regions/)中可用。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

可以使用 Azure Cloud Shell 完成本教程，或者在本地运行相应的命令。  如果你未曾用过 Azure Cloud Shell，请[立即登录](https://shell.azure.com)以完成初始设置。
如果选择在本地运行这些命令，则需要安装 CLI。  本教程要求运行 Azure CLI 2.0.71 或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。


## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](https://docs.microsoft.com/cli/azure/group) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在 **eastus2** 位置创建名为 **myResourceGroupNAT** 的资源组。

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>创建 NAT 网关

### <a name="create-a-public-ip-address"></a>创建公共 IP 地址

若要访问公共 Internet，需要提供 NAT 网关的一个或多个公共 IP 地址。 使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) 在 **myResourceGroupNAT** 中创建名为 **myPublicIP** 的公共 IP 地址资源。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>创建公共 IP 前缀

可对 NAT 网关使用一个或多个公共 IP 地址资源和/或公共 IP 前缀。 为方便演示，我们将一个公共 IP 前缀资源添加到此方案。   使用 [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPprefix** 的公共 IP 前缀资源。

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>创建 NAT 网关资源

本部分详细介绍如何使用 NAT 网关资源创建并配置 NAT 服务的以下组件：
  - 一个公共 IP 池和公共 IP 前缀，供 NAT 网关资源转换的出站流使用。
  - 将空闲超时从默认值 4 分钟更改为 10 分钟。

使用 [az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) 创建名为 **myNATgateway** 的全局 Azure NAT 网关。 该命令同时使用公共 IP 地址 **myPublicIP** 和公共 IP 前缀 **myPublicIPprefix**。 该命令将空闲超时更改为 **10** 分钟。

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

此时，NAT 网关可正常工作，唯一遗漏的操作就是配置虚拟网络的哪些子网应使用该网关。

## <a name="configure-virtual-network"></a>配置虚拟网络

在部署 VM 并使用 NAT 网关之前，需要先创建虚拟网络。

使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) 在 **myResourceGroupNAT** 中创建名为 **myVnet** 的虚拟网络，该虚拟网络包含名为 **mySubnet** 的子网。  虚拟网络的 IP 地址空间为 **192.168.0.0/16**。 虚拟网络中的子网为 **192.168.0.0/24**。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>配置源子网的 NAT 服务

我们将使用 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet) 在虚拟网络 **myVnet** 中配置源子网 **mySubnet**，以使用特定的 NAT 网关资源 **myNATgateway**。  此命令将激活指定子网中的 NAT 服务。

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

发往 Internet 目标的所有出站流量现在将使用该 NAT 网关。  无需配置 UDR。

## <a name="create-a-vm-to-use-the-nat-service"></a>创建 VM 以使用 NAT 服务

现在，我们将创建一个 VM 来使用 NAT 服务。  此 VM 将某个公共 IP 用作实例级公共 IP，使你能够访问此 VM。  NAT 服务可识别流的方向，并会替代子网中的默认 Internet 目标。 VM 的公共 IP 地址不会用于出站连接。

### <a name="create-public-ip-for-source-vm"></a>创建源 VM 的公共 IP

我们将创建一个用于访问 VM 的公共 IP。  使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPVM** 的公共 IP 地址资源。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>创建 VM 的 NSG

由于标准公共 IP 地址是“默认安全的”，因此我们需要创建一个 NSG 来允许 SSH 入站访问。 使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) 在 **myResourceGroupNAT** 中创建名为 **myNSG** 的 NSG 资源。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>在源 VM 上公开 SSH 终结点

我们将在 NSG 中创建一个规则，以通过 SSH 访问源 VM。 使用 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) 在 **myResourceGroupNAT** 中名为 **myNSG** 的 NSG 规则内创建名为 **ssh** 的 NSG 规则。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>创建 VM 的 NIC

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 创建一个网络接口，并将其关联到公共 IP 地址和网络安全组。 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>创建 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 创建虚拟机。  我们将为此 VM 生成 SSH 密钥，并存储私钥供稍后使用。

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

等待 VM 部署完成，然后继续执行剩余的步骤。

## <a name="discover-the-ip-address-of-the-vm"></a>发现 VM 的 IP 地址

首先需要发现已创建的 VM 的 IP 地址。 若要检索 VM 的公共 IP 地址，请使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>复制公共 IP 地址并将其粘贴到记事本中，以便可以用它来访问 VM。

### <a name="sign-in-to-vm"></a>登录到 VM

应将 SSH 凭据存储在上一操作中运行的 Cloud Shell 中。  在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一步骤中检索到的 IP 地址通过 SSH 连接到虚拟机。

```bash
ssh <ip-address-destination>
```

现已准备好使用 NAT 服务。

## <a name="clean-up-resources"></a>清理资源

如果不再需要上述资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令将其删除。

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个 NAT 网关，并创建了一个 VM 来使用该网关。 

可以查看 Azure Monitor 中的指标来了解 NAT 服务的运行情况。 可以诊断可用 SNAT 端口资源耗尽等问题。  添加更多公共 IP 地址资源和/或公共 IP 前缀资源即可解决 SNAT 端口资源耗尽的问题。


- 了解 [Azure 虚拟网络 NAT](./nat-overview.md)
- 了解 [NAT 网关资源](./nat-gateway-resource.md)。
- 有关[使用 Azure CLI 部署 NAT 网关资源](./quickstart-create-nat-gateway-cli.md)的快速入门。
- 有关[使用 Azure PowerShell 部署 NAT 网关资源](./quickstart-create-nat-gateway-powershell.md)的快速入门。
- 有关[使用 Azure 门户部署 NAT 网关资源](./quickstart-create-nat-gateway-portal.md)的快速入门。
- [提供有关公共预览版的反馈](https://aka.ms/natfeedback)。
> [!div class="nextstepaction"]

