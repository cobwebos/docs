---
title: 教程：创建并测试 NAT 网关 - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: 本教程介绍如何使用 Azure CLI 创建 NAT 网关并测试 NAT 服务
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 988bd6dbb157276a9ee37c8ca3051a808f8b6499
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661067"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>教程：使用 Azure CLI 创建 NAT 网关并测试 NAT 服务

在本教程中，你将创建一个 NAT 网关来为 Azure 中的虚拟机提供出站连接。 为了测试该 NAT 网关，你将部署源和目标虚拟机。 通过与公共 IP 地址建立出站连接来测试 NAT 网关。 将从源虚拟机到目标虚拟机建立这些连接。 为简单起见，本教程将源和目标部署在同一资源组中的两个不同虚拟网络内。

>[!NOTE] 
>Azure 虚拟网络 NAT 目前以公共预览版提供，仅在有限的几个[区域](./nat-overview.md#region-availability)中可用。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

可以使用 Azure Cloud Shell 完成本教程，或者在本地运行相应的命令。  如果你未曾用过 Azure Cloud Shell，请[立即登录](https://shell.azure.com)。

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

若要访问公共 Internet，需要提供 NAT 网关的一个或多个公共 IP 地址。 使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPsource** 的公共 IP 地址资源。

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>创建公共 IP 前缀

可对 NAT 网关使用一个或多个公共 IP 地址资源和/或公共 IP 前缀。 为方便演示，我们将一个公共 IP 前缀资源添加到此方案。   使用 [az network public-ip prefix create](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPprefixsource** 的公共 IP 前缀资源。

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>创建 NAT 网关资源

本部分详细介绍如何使用 NAT 网关资源创建并配置 NAT 服务的以下组件：
  - 一个公共 IP 池和公共 IP 前缀，供 NAT 网关资源转换的出站流使用。
  - 将空闲超时从默认值 4 分钟更改为 10 分钟。

使用 [az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) 创建名为 **myNATgateway** 的全局 Azure NAT 网关。 该命令同时使用公共 IP 地址 **myPublicIP** 和公共 IP 前缀 **myPublicIPprefix**。 该命令还将空闲超时更改为 10 分钟。

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

此时，NAT 网关可正常工作，唯一遗漏的操作就是配置虚拟网络的哪些子网应使用该网关。

## <a name="prepare-the-source-for-outbound-traffic"></a>准备出站流量的源

本文将引导你设置整个测试环境。 你将使用开源工具来设置测试，以验证 NAT 网关。 我们从源开始，它使用我们前面创建的 NAT 网关。

### <a name="configure-virtual-network-for-source"></a>配置源的虚拟网络

在部署 VM 并测试 NAT 网关之前，需要先创建虚拟网络。

使用 [az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet)，在 **myResourceGroupNAT** 中创建名为 **myVnetsource** 的虚拟网络和名为 **mySubnetsource** 的子网。  虚拟网络的 IP 地址空间为 **192.168.0.0/16**。 虚拟网络中的子网为 **192.168.0.0/24**。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>配置源子网的 NAT 服务

使用 [az network Microsoft Azure Virtual Network subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet)，将虚拟网络 **myVnetsource** 中的源子网 **mySubnetsource** 配置为使用特定的 NAT 网关资源 **myNATgateway**。 此命令将激活指定子网中的 NAT 服务。

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

发往 Internet 目标的所有出站流量现在将使用该 NAT 服务。  无需配置 UDR。

在测试 NAT 网关之前，需要先创建源 VM。  将某个公共 IP 地址资源分配为实例级公共 IP，以便从外部访问此 VM。 此地址仅用于访问此 VM，以进行测试。  我们将演示 NAT 服务如何优先于其他出站选项。

也可以不使用公共 IP 创建此 VM，而是在练习中创建另一个 VM 作为没有公共 IP 的 Jumpbox。

### <a name="create-public-ip-for-source-vm"></a>创建源 VM 的公共 IP

我们将创建一个用于访问源 VM 的公共 IP。 使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPsourceVM** 的公共 IP 地址资源。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>创建源 VM 的 NSG

由于标准公共 IP 地址是“默认安全的”，因此我们需要创建一个 NSG 来允许 SSH 入站访问。  Azure NAT 服务可以识别流的方向。 在同一子网中配置 NAT 网关后，此 NSG 不会用于出站连接。 使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) 在 **myResourceGroupNAT** 中创建名为 **myNSGsource** 的 NSG 资源。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>在源 VM 上公开 SSH 终结点

我们将在 NSG 中创建一个规则，以通过 SSH 访问源 VM。 使用 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) 创建名为 **ssh** 的 NSG 规则。 将在资源组 **myResourceGroupNAT** 中名为 **myNSGsource** 的 NSG 内创建此规则。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-source-vm"></a>创建源 VM 的 NIC

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 创建一个网络接口，并将其关联到公共 IP 地址和网络安全组。 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>创建源 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 创建虚拟机。  我们将为此 VM 生成 SSH 密钥，并存储私钥供稍后使用。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

该命令将立即返回，但部署 VM 可能需要花费几分钟时间。

## <a name="prepare-destination-for-outbound-traffic"></a>准备出站流量的目标

现在，我们将为 NAT 服务转换的出站流量创建目标，以便对其进行测试。

### <a name="configure-virtual-network-for-destination"></a>配置目标的虚拟网络

 我们需要创建一个虚拟网络，目标虚拟机将置于其中。  这些命令执行的步骤与针对源 VM 执行的步骤相同，只是需要做出轻微的更改来公开目标终结点。

使用 [az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet)，在 **myResourceGroupNAT** 中创建名为 **myVnetdestination** 的虚拟网络和名为 **mySubnetdestination** 的子网。  虚拟网络的 IP 地址空间为 **192.168.0.0/16**。 虚拟网络中的子网为 **192.168.0.0/24**。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>创建目标 VM 的公共 IP

我们将创建一个用于访问源 VM 的公共 IP。 使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPdestinationVM** 的公共 IP 地址资源。 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard \
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>创建目标 VM 的 NSG

标准公共 IP 地址是“默认安全的”，因此需要创建一个 NSG 来允许 SSH 入站访问。 Azure NAT 服务可以识别流的方向。 在同一子网中配置 NAT 网关后，此 NSG 不会用于出站连接。 使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) 在 **myResourceGroupNAT** 中创建名为 **myNSGdestination** 的 NSG 资源。

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination \
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>在目标 VM 上公开 SSH 终结点

我们将在 NSG 中创建一个规则，以通过 SSH 访问目标 VM。 使用 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) 创建名为 **ssh** 的 NSG 规则。 将在资源组 **myResourceGroupNAT** 中名为 **myNSGdestination** 的 NSG 内创建此规则。

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>在目标 VM 上公开 HTTP 终结点

我们将在 NSG 中创建一个规则，以通过 HTTP 访问目标 VM。 使用 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) 在 **myResourceGroupNAT** 中名为 **myNSGdestination** 的 NSG 规则内创建名为 **http** 的 NSG 规则。

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
```

### <a name="create-nic-for-destination-vm"></a>创建目标 VM 的 NIC

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 创建一个网络接口，并将其关联到公共 IP 地址 **myPublicIPdestinationVM** 和网络安全组 **myNSGdestination**。 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination \
    --location westus
```

### <a name="create-a-destination-vm"></a>创建目标 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 创建虚拟机。  我们将为此 VM 生成 SSH 密钥，并存储私钥供稍后使用。

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait \
    --location westus
```
该命令将立即返回，但部署 VM 可能需要花费几分钟时间。

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>在目标 VM 上准备 Web 服务器和测试有效负载

首先需要发现目标 VM 的 IP 地址。  若要获取目标 VM 的公共 IP 地址，请使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>复制该公共 IP 地址并将其粘贴到记事本中，以便可以在后续步骤中使用它。 指明这是目标虚拟机。

### <a name="sign-in-to-destination-vm"></a>登录到目标 VM

应将 SSH 凭据存储在上一操作中运行的 Cloud Shell 中。  在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一步骤中检索到的 IP 地址通过 SSH 连接到虚拟机。 

```bash
ssh <ip-address-destination>
```

登录后，复制并粘贴以下命令。  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

这些命令将更新虚拟机，安装 nginx，并创建 100 KB 大小的文件。 将使用 NAT 服务从源 VM 中检索此文件。

关闭与目标 VM 建立的 SSH 会话。

## <a name="prepare-test-on-source-vm"></a>在源 VM 上准备测试

首先需要发现源 VM 的 IP 地址。  若要获取源 VM 的公共 IP 地址，请使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>复制该公共 IP 地址并将其粘贴到记事本中，以便可以在后续步骤中使用它。 指明这是源虚拟机。

### <a name="sign-in-to-source-vm"></a>登录到源 VM

同样，SSH 凭据应存储在 Cloud Shell 中。 在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com) 的新选项卡。  使用在上一步骤中检索到的 IP 地址通过 SSH 连接到虚拟机。 

```bash
ssh <ip-address-source>
```

复制并粘贴以下命令，以准备测试 NAT 服务。

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

此命令将更新虚拟机，安装 go，安装 GitHub 中的 [hey](https://github.com/rakyll/hey)，并更新 shell 环境。

现已准备好测试 NAT 服务。

## <a name="validate-nat-service"></a>验证 NAT 服务

登录到源 VM 后，可以使用 **curl** 和 **hey** 生成发往目标 IP 地址的请求。

使用 curl 检索 100 KB 大小的文件。  请将以下示例中的 **\<ip-address-destination>** 替换为前面复制的目标 IP 地址。  **--output** 参数指示将丢弃检索到的文件。

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

也可以使用 **hey** 生成一系列请求。 同样，请将 **\<ip-address-destination>** 替换为前面复制的目标 IP 地址。

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

此命令将生成 100 个请求，其中有 10 个是超时为 30 秒的并发请求。 不会重复使用 TCP 连接。  每个请求将检索 100 KB。  运行结束时，**hey** 会报告有关 NAT 服务运行情况的统计信息。

## <a name="clean-up-resources"></a>清理资源

如果不再需要上述资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令将其删除。

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>后续步骤
在本教程中，你已创建 NAT 网关、源 VM 和目标 VM，然后测试了 NAT 网关。

可以查看 Azure Monitor 中的指标来了解 NAT 服务的运行情况。 可以诊断可用 SNAT 端口资源耗尽等问题。  通过添加更多公共 IP 地址资源和/或公共 IP 前缀资源，可以轻松解决 SNAT 端口资源耗尽的问题。

- 了解[虚拟网络 NAT](./nat-overview.md)。
- 了解 [NAT 网关资源](./nat-gateway-resource.md)。
- 有关[使用 Azure CLI 部署 NAT 网关资源](./quickstart-create-nat-gateway-cli.md)的快速入门。
- 有关[使用 Azure PowerShell 部署 NAT 网关资源](./quickstart-create-nat-gateway-powershell.md)的快速入门。
- 有关[使用 Azure 门户部署 NAT 网关资源](./quickstart-create-nat-gateway-portal.md)的快速入门。
- [提供有关公共预览版的反馈](https://aka.ms/natfeedback)。

> [!div class="nextstepaction"]

