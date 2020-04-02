---
title: 使用 PowerShell 部署 Azure Spot VM
description: 了解如何使用 Azure PowerShell 部署 Spot VM 以节省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 234cf3f51173c53ef8ca15af4ca6f24881be3109
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547284"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>使用 Azure PowerShell 部署 Spot VM


使用[Spot VM](spot-vms.md)使您能够利用我们未使用的容量，从而显著节省成本。 在 Azure 需要返回容量的任何时间点，Azure 基础结构将驱逐 Spot VM。 因此，Spot VM 非常适合处理批处理作业、开发/测试环境、大型计算工作负载等中断的工作负载。

现货 VM 的定价基于区域和 SKU 是可变的。 有关详细信息，请参阅[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 有关设置最高价格的详细信息，请参阅[现货 VM - 定价](spot-vms.md#pricing)。

您可以选择为 VM 设置您愿意每小时支付的最高价格。 Spot VM 的最高价格可以用美元 （USD） 设置，最多使用 5 个小数位。 例如，该值`0.98765`将是每小时 0.98765 美元的最高价格。 如果将最高价格设置为`-1`，则 VM 不会根据价格被逐出。 VM 的价格将是现货的当前价格或标准 VM 的价格，只要有容量和配额可用，标准 VM 的价格就更少了。


## <a name="create-the-vm"></a>创建 VM

使用[New-AzVmConfig 创建](/powershell/module/az.compute/new-azvmconfig)点 VM 以创建配置。 包括`-Priority Spot`并设置为`-MaxPrice`：
- `-1`因此，VM 不会根据价格被逐出。
- 一美元金额，最多 5 位。 例如，`-MaxPrice .98765`这意味着一旦 spotVM 的价格达到每小时 0.98765 美元，VM 将被处理。


此示例创建一个不根据定价进行处理的 spotVM（仅当 Azure 需要返回容量时）。

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

创建 VM 后，可以查询以查看资源组中所有 VM 的最大价格。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>后续步骤

您还可以使用[Azure CLI](../linux/spot-cli.md)或[模板](../linux/spot-template.md)创建 Spot VM。

如果遇到错误，请参阅[错误代码](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。