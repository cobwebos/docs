---
title: 快速入门：创建 NAT 网关 - Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: 本快速入门介绍如何使用 Azure PowerShell 创建 NAT 网关
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: fadf42e7bd7d754dea64542f06866a439eb460ec
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588750"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建 NAT 网关

本快速入门介绍如何使用 Azure 虚拟网络 NAT 服务。 你将创建一个 NAT 网关，以便为 Azure 中的虚拟机提供出站连接。 

>[!NOTE] 
>Azure 虚拟网络 NAT 目前以公共预览版提供，仅在有限的几个[区域](https://azure.microsoft.com/global-infrastructure/regions/)中可用。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

可以使用 Azure Cloud Shell 完成本教程，或者在本地运行命令。  如果你未曾用过 Azure Cloud Shell，请[立即登录](https://shell.azure.com)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在 **eastus2** 位置创建名为 **myResourceGroupNAT** 的资源组。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>创建 NAT 网关

NAT 网关的公共 IP 选项为：

* **公共 IP 地址**
* **公共 IP 前缀**

两者均可在 NAT 网关中使用。

为方便演示，我们将在此方案中添加公共 IP 地址和公共 IP 前缀。

### <a name="create-a-public-ip-address"></a>创建公共 IP 地址

若要访问 Internet，需要提供 NAT 网关的一个或多个公共 IP 地址。 使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) 在 **myResourceGroupNAT** 中创建名为 **myPublicIP** 的公共 IP 地址资源。 此命令的结果将存储在 **$publicIP** 变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>创建公共 IP 前缀

使用 [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPprefix** 的公共 IP 前缀资源。  此命令的结果将存储在名为 **$publicIPPrefix** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>创建 NAT 网关资源

本部分详细介绍如何使用 NAT 网关资源创建并配置 NAT 服务的以下组件：
  - 一个公共 IP 池和公共 IP 前缀，供 NAT 网关资源转换的出站流使用。
  - 将空闲超时从默认值 4 分钟更改为 10 分钟。

使用 [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway) 创建全局 Azure NAT 网关。 此命令的结果将创建名为 **myNATgateway** 的网关资源，该资源使用公共 IP 地址 **myPublicIP** 和公共 IP 前缀 **myPublicIPprefix**。 空闲超时设置为 10 分钟。  此命令的结果将存储在名为 **$natGateway** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

此时，NAT 网关可正常工作，唯一遗漏的操作就是配置虚拟网络的哪些子网应使用该网关。

## <a name="configure-virtual-network"></a>配置虚拟网络

创建虚拟网络，并将子网关联到网关。

使用 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) 在 **myResourceGroup** 中创建名为 **myVnet** 的虚拟网络，并使用 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) 创建名为 **mySubnet** 的子网。 虚拟网络的 IP 地址空间为 **192.168.0.0/16**。 虚拟网络中的子网为 **192.168.0.0/24**。  命令的结果将存储在名为 **$subnet** 和 **$vnet** 的变量中，供稍后使用。

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

发往 Internet 目标的所有出站流量现在将使用该 NAT 服务。  无需配置 UDR。

## <a name="create-a-vm-to-use-the-nat-service"></a>创建 VM 以使用 NAT 服务

现在，我们将创建一个 VM 来使用 NAT 服务。  此 VM 将某个公共 IP 用作实例级公共 IP，使你能够访问此 VM。  NAT 服务可识别流的方向，并会替代子网中的默认 Internet 目标。 VM 的公共 IP 地址不会用于出站连接。

### <a name="create-public-ip-for-source-vm"></a>创建源 VM 的公共 IP

我们将创建一个用于访问 VM 的公共 IP。  使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPVM** 的公共 IP 地址资源。  此命令的结果将存储在名为 **$publicIpVM** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>创建 NSG 并公开 VM 的 SSH 终结点

标准公共 IP 地址是“默认安全的”，因此需要创建一个 NSG 来允许 SSH 入站访问。 使用 [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) 创建名为 **myNSG** 的 NSG 资源。 使用 [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) 在 **myResourceGroupNAT** 中创建名为 **ssh** 的 NSG 规则用于进行 SSH 访问。  此命令的结果将存储在名为 **$nsg** 的变量中，供稍后使用。

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>创建 VM 的 NIC

使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) 创建名为 **myNic** 的网络接口。 此命令会将公共 IP 地址与网络安全组相关联。 此命令的结果将存储在名为 **$nic** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>创建 VM

#### <a name="create-ssh-key-pair"></a>创建 SSH 密钥对

需要一个 SSH 密钥对才能完成本快速入门。 如果已有一个 SSH 密钥对，则可以跳过此步骤。

使用 ssh-keygen 创建 SSH 密钥对。

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
有关如何创建 SSH 密钥对的更多详细信息，包括 PuTTy 的用法，请参阅[如何将 SSH 密钥与 Windows 配合使用](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

如果使用 Cloud Shell 创建 SSH 密钥对，则密钥对将存储在容器映像中。 系统会[自动创建此存储帐户](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)。 只有在检索密钥之后，才能删除该存储帐户或其中的文件共享。

#### <a name="create-vm-configuration"></a>创建 VM 配置

若要在 PowerShell 中创建 VM，请创建一个配置，其中包含要使用的映像、大小和身份验证选项设置。 系统将使用该配置生成 VM。

定义 SSH 凭据、OS 信息和 VM 大小。 在此示例中，SSH 密钥存储在 ~/.ssh/id_rsa.pub 中。

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
合并配置定义，使用 [New-AzVM]((https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azps-2.8.0)) 在 **myResourceGroupNAT** 中创建名为 **myVM** 的 VM。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

等待 VM 做好部署准备，然后继续执行剩余的步骤。

## <a name="discover-the-ip-address-of-the-vm"></a>发现 VM 的 IP 地址

首先需要发现已创建的 VM 的 IP 地址。 若要获取 VM 的公共 IP 地址，请使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)。 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>复制公共 IP 地址并将其粘贴到记事本中，以便可以用它来访问 VM。

### <a name="sign-in-to-vm"></a>登录到 VM

应将 SSH 凭据存储在上一操作中运行的 Cloud Shell 中。  在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一步骤中检索到的 IP 地址通过 SSH 连接到虚拟机。

```bash
ssh azureuser@<ip-address-destination>
```

现已准备好使用 NAT 服务。

## <a name="clean-up-resources"></a>清理资源

如果不再需要上述资源组及其包含的所有资源，可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) 命令将其删除。

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
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


