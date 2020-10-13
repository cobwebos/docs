---
title: 使用 PowerShell 部署 Azure 点 Vm
description: 了解如何使用 Azure PowerShell 部署专色 Vm 以节省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 44d23710db169fa27aaba8928d421918bef93fec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825129"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>使用 Azure PowerShell 部署专色 Vm


使用 [污点 vm](../spot-vms.md) ，你可以显著节省成本。 当 Azure 需要恢复容量时，Azure 基础结构将逐出点 Vm。 因此，专色 Vm 非常适合用于处理中断的工作负荷，如批处理作业、开发/测试环境、大型计算工作负荷等。

基于区域和 SKU，污点 Vm 的定价是可变的。 有关详细信息，请参阅适用于 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 有关设置最大价格的详细信息，请参阅 [污点 vm-定价](../spot-vms.md#pricing)。

你可以选择为 VM 设置你愿意支付的最大价格（每小时）。 可使用最多5个小数位 (USD) ，为专色 VM 设置最大价格。 例如，值 `0.98765` 表示最高价格为 0.98765 美元/小时。 如果将最大价格设置为 `-1` ，则不会根据价格收回 VM。 VM 的价格将是当前的价格价格或标准 VM 的价格，只要容量和配额可用，此价格就越小。


## <a name="create-the-vm"></a>创建 VM

使用 [AzVmConfig](/powershell/module/az.compute/new-azvmconfig) 创建 spotVM，创建配置。 包含 `-Priority Spot` 并将设置 `-MaxPrice` 为：
- `-1` 因此，VM 不会根据价格收回。
- 美元金额，最多5位数字。 例如， `-MaxPrice .98765` 这意味着，一旦 spotVM 的价格约为每小时98765美元，就会解除分配 VM。


此示例将创建一个 spotVM，该仅在 Azure 需要容量回) 时才会基于定价 (释放。 逐出策略设置为解除分配 VM，以便以后可以重新启动它。 如果要在逐出 VM 时删除 VM 和基础磁盘，请 `-EvictionPolicy` 在中将设置为 `Delete` `New-AzVMConfig` 。


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

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

创建 VM 后，你可以查询以查看资源组中所有 Vm 的最大价格。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>模拟逐出

可以模拟某个点 VM 的 [逐出](/rest/api/compute/virtualmachines/simulateeviction) ，以测试应用程序将 repond 突然逐出的程度。 

将以下内容替换为你的信息： 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>后续步骤

你还可以使用 [Azure CLI](../linux/spot-cli.md)、 [门户](../spot-portal.md) 或 [模板](../linux/spot-template.md)创建一个专色 VM。

使用 [Azure 零售价格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) 查询当前定价信息，获取有关专色定价的信息。 `meterName`和 `skuName` 都包含 `Spot` 。

如果遇到错误，请参阅 [错误代码](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。