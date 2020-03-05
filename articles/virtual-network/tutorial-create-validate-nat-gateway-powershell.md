---
title: 教程：创建并测试 NAT 网关 - Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: 本教程介绍如何使用 Azure PowerShell 创建 NAT 网关并测试 NAT 服务
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: faaf32b08fdf2415b1c60192f917fc2aedc14704
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660982"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>教程：使用 Azure PowerShell 创建 NAT 网关并测试 NAT 服务

在本教程中，你将创建一个 NAT 网关来为 Azure 中的虚拟机提供出站连接。 为了测试该 NAT 网关，你将部署源和目标虚拟机。 通过与公共 IP 地址建立出站连接来测试 NAT 网关。 将从源虚拟机到目标虚拟机建立这些连接。 为简单起见，本教程将源和目标部署在同一资源组中的两个不同虚拟网络内。

>[!NOTE] 
>Azure 虚拟网络 NAT 目前以公共预览版提供，仅在有限的几个[区域](./nat-overview.md#region-availability)中可用。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

可以使用 Azure Cloud Shell 完成本教程，或者在本地运行相应的命令。  如果你未曾用过 Azure Cloud Shell，请[立即登录](https://shell.azure.com)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](https://docs.microsoft.com/cli/azure/group) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在 **eastus2** 位置创建名为 **myResourceGroupNAT** 的资源组。


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>创建 NAT 网关

### <a name="create-a-public-ip-address"></a>创建公共 IP 地址

若要访问 Internet，需要提供 NAT 网关的一个或多个公共 IP 地址。 使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPsource** 的公共 IP 地址资源。 此命令的结果将存储在名为 **$publicIPsource** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>创建公共 IP 前缀

 使用 [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPprefixsource** 的公共 IP 前缀资源。  此命令的结果将存储在名为 **$publicIPPrefixsource** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>创建 NAT 网关资源

本部分详细介绍如何使用 NAT 网关资源创建并配置 NAT 服务的以下组件：
  - 一个公共 IP 池和公共 IP 前缀，供 NAT 网关资源转换的出站流使用。
  - 将空闲超时从默认值 4 分钟更改为 10 分钟。

使用 [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway) 创建全局 Azure NAT 网关。 此命令的结果将创建名为 **myNATgateway** 的网关资源，该资源使用公共 IP 地址 **myPublicIPsource** 和公共 IP 前缀 **myPublicIPprefixsource**。 空闲超时设置为 10 分钟。  此命令的结果将存储在名为 **$natGateway** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

此时，NAT 网关可正常工作，唯一遗漏的操作就是配置虚拟网络的哪些子网应使用该网关。

## <a name="prepare-the-source-for-outbound-traffic"></a>准备出站流量的源

本文将引导你设置整个测试环境。 你将使用开源工具来设置测试，以验证 NAT 网关。 我们从源开始，它使用我们前面创建的 NAT 网关。

### <a name="configure-virtual-network-for-source"></a>配置源的虚拟网络

创建虚拟网络，并将子网关联到网关。

使用 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) 在 **myResourceGroupNAT** 中创建名为 **myVnetsource** 的虚拟网络，并使用 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) 创建名为 **mySubnetsource** 的子网。 虚拟网络的 IP 地址空间为 **192.168.0.0/16**。 虚拟网络中的子网为 **192.168.0.0/24**。  命令的结果将存储在名为 **$subnetsource** 和 **$vnetsource** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

发往 Internet 目标的所有出站流量现在将使用该 NAT 服务。  无需配置 UDR。

在测试 NAT 网关之前，需要先创建源 VM。  将某个公共 IP 地址资源分配为实例级公共 IP，以便从外部访问此 VM。 此地址仅用于访问此 VM，以进行测试。  我们将演示 NAT 服务如何优先于其他出站选项。

也可以不使用公共 IP 创建此 VM，而是在练习中创建另一个 VM 作为没有公共 IP 的 Jumpbox。

### <a name="create-public-ip-for-source-vm"></a>创建源 VM 的公共 IP

我们将创建一个用于访问 VM 的公共 IP。  使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPVM** 的公共 IP 地址资源。  此命令的结果将存储在名为 **$publicIpsourceVM** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>创建 NSG 并公开 VM 的 SSH 终结点

由于标准公共 IP 地址是“默认安全的”，因此我们需要创建一个 NSG 来允许 SSH 入站访问。 NAT 服务可以识别流的方向。 在同一子网中配置 NAT 网关后，此 NSG 不会用于出站连接。 使用 [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) 创建名为 **myNSGsource** 的 NSG 资源。 使用 [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) 在 **myResourceGroupNAT** 中创建名为 **ssh** 的 NSG 规则用于进行 SSH 访问。 此命令的结果将存储在名为 **$nsgsource** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>创建源 VM 的 NIC

使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) 创建名为 **myNicsource** 的网络接口。 此命令会将公共 IP 地址与网络安全组相关联。 此命令的结果将存储在名为 **$nicsource** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>创建源 VM

#### <a name="create-ssh-key-pair"></a>创建 SSH 密钥对

需要一个 SSH 密钥对才能完成本快速入门。 如果已有一个 SSH 密钥对，则可以跳过此步骤。

使用 ssh-keygen 创建 SSH 密钥对。

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
有关如何创建 SSH 密钥对的更多详细信息，包括 PuTTy 的用法，请参阅[如何将 SSH 密钥与 Windows 配合使用](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

如果使用 Cloud Shell 创建 SSH 密钥对，则密钥对将存储在容器映像中。 系统会[自动创建此存储帐户](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)。 只有在检索密钥之后，才能删除该存储帐户或其中的文件共享。

#### <a name="create-vm-configuration"></a>创建 VM 配置

若要在 PowerShell 中创建 VM，请创建一个配置，其中包含要使用的映像、大小和身份验证选项等设置。 然后，系统会使用此配置来生成 VM。

定义 SSH 凭据、OS 信息和 VM 大小。 在此示例中，SSH 密钥存储在 ~/.ssh/id_rsa.pub 中。

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
合并配置定义，使用 [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) 在 **myResourceGroupNAT** 中创建名为 **myVMsource** 的 VM。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

该命令将立即返回，但部署 VM 可能需要花费几分钟时间。

## <a name="prepare-destination-for-outbound-traffic"></a>准备出站流量的目标

现在，我们将为 NAT 服务转换的出站流量创建目标，以便对其进行测试。

### <a name="configure-virtual-network-for-destination"></a>配置目标的虚拟网络

我们需要创建一个虚拟网络，目标虚拟机将置于其中。  这些命令执行的步骤与针对源 VM 执行的步骤相同。 我们已做出轻微的更改来公开目标终结点。

使用 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) 在 **myResourceGroupNAT** 中创建名为 **myVnetdestination** 的虚拟网络，并使用 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) 创建名为 **mySubnetdestination** 的子网。 虚拟网络的 IP 地址空间为 **192.168.0.0/16**。 虚拟网络中的子网为 **192.168.0.0/24**。  命令的结果将存储在名为 **$subnetdestination** 和 **$vnetdestination** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>创建目标 VM 的公共 IP

我们将创建一个用于访问源 VM 的公共 IP。  使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) 在 **myResourceGroupNAT** 中创建名为 **myPublicIPdestinationVM** 的公共 IP 地址资源。  此命令的结果将存储在名为 **$publicIpdestinationVM** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>创建 NSG 并公开 VM 的 SSH 和 HTTP 终结点

标准公共 IP 地址是“默认安全的”，因此我们需要创建一个 NSG 来允许 SSH 入站访问。 使用 [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) 创建名为 **myNSGdestination** 的 NSG 资源。 使用 [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) 创建名为 **ssh** 的 NSG 规则用于进行 SSH 访问。  使用 [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) 创建名为 **http** 的 NSG 规则用于进行 HTTP 访问。 这两个规则将在 **myResourceGroupNAT** 中创建。 此命令的结果将存储在名为 **$nsgdestination** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>创建目标 VM 的 NIC

使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) 创建名为 **myNicdestination** 的网络接口。 此命令会将公共 IP 地址与网络安全组相关联。 此命令的结果将存储在名为 **$nicdestination** 的变量中，供稍后使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>创建目标 VM

#### <a name="create-vm-configuration"></a>创建 VM 配置

若要在 PowerShell 中创建 VM，请创建一个配置，其中包含要使用的映像、大小和身份验证选项等设置。 然后，系统会使用此配置来生成 VM。

定义 SSH 凭据、OS 信息和 VM 大小。 在此示例中，SSH 密钥存储在 ~/.ssh/id_rsa.pub 中。

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
合并配置定义，使用 [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) 在 **myResourceGroupNAT** 中创建名为 **myVMdestination** 的 VM。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

该命令将立即返回，但部署 VM 可能需要花费几分钟时间。

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>在目标 VM 上准备 Web 服务器和测试有效负载

首先需要发现目标 VM 的 IP 地址。  若要获取 VM 的公共 IP 地址，请使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)。 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>复制该公共 IP 地址并将其粘贴到记事本中，以便可以在后续步骤中使用它。 指明这是目标虚拟机。

### <a name="sign-in-to-destination-vm"></a>登录到目标 VM

应将 SSH 凭据存储在上一操作中运行的 Cloud Shell 中。  在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一步骤中检索到的 IP 地址通过 SSH 连接到虚拟机。 

```bash
ssh azureuser@<ip-address-destination>
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

首先需要发现源 VM 的 IP 地址。  若要获取 VM 的公共 IP 地址，请使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)。 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>复制该公共 IP 地址并将其粘贴到记事本中，以便可以在后续步骤中使用它。 指明这是源虚拟机。

### <a name="log-into-source-vm"></a>登录到源 VM

同样，SSH 凭据应存储在 Cloud Shell 中。 在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com) 的新选项卡。  使用在上一步骤中检索到的 IP 地址通过 SSH 连接到虚拟机。 

```bash
ssh azureuser@<ip-address-source>
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

这些命令将更新虚拟机，安装 go，安装 GitHub 中的 [hey](https://github.com/rakyll/hey)，并更新 shell 环境。

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

如果不再需要上述资源组及其包含的所有资源，可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) 命令将其删除。

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
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

