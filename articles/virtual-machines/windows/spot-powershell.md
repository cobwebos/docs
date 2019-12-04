---
title: 使用 PowerShell 部署 Azure 点 Vm
description: 了解如何使用 Azure PowerShell 部署专色 Vm 以节省成本。
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/14/2019
ms.author: cynthn
ms.openlocfilehash: 8752522e4b5a7b91778d6eb2cd8e4ba3bac95da0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782120"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>预览：使用 Azure PowerShell 部署专色 Vm


使用[污点 vm](spot-vms.md) ，你可以显著节省成本。 当 Azure 需要恢复容量时，Azure 基础结构将逐出点 Vm。 因此，专色 Vm 非常适合用于处理中断的工作负荷，如批处理作业、开发/测试环境、大型计算工作负荷等。

基于区域和 SKU，污点 Vm 的定价是可变的。 有关详细信息，请参阅适用于[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 有关设置最大价格的详细信息，请参阅[污点 vm-定价](spot-vms.md#pricing)。

你可以选择为 VM 设置你愿意支付的最大价格（每小时）。 可使用最多5个小数位来设置专色 VM 的最大价格（美元）。 例如，值 `0.98765`的最大价格为 $0.98765 美元/小时。 如果将最大价格设置为 `-1`，则不会根据价格收回 VM。 VM 的价格将是当前的价格价格或标准 VM 的价格，只要容量和配额可用，此价格就越小。

> [!IMPORTANT]
> 污点实例当前为公共预览版。
> 不建议将此预览版本用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 对于公共预览版的早期部分，污点实例将具有固定价格，因此不会有任何基于价格的逐出。


## <a name="create-the-vm"></a>创建 VM

使用[AzVmConfig](/powershell/module/az.compute/new-azvmconfig)创建 spotVM，创建配置。 包括 `-Priority Spot`，并将 `-MaxPrice` 设置为：
- `-1` 因此不会根据价格收回 VM。
- 美元金额，最多5位数字。 例如，`-MaxPrice .98765` 表示一旦 spotVM 的价格约为每小时98765美元，就会释放 VM。

> [!IMPORTANT]
> 对于公共预览版的早期部分，您可以设置最大价格，但会被忽略。 污点 Vm 具有固定价格，因此不会有任何基于价格的逐出。


此示例将创建一个 spotVM，此功能不会根据定价进行释放（仅当 Azure 需要恢复容量时）。

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

创建 VM 后，你可以查询以查看资源组中所有 Vm 的最大价格。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>后续步骤

你还可以使用[Azure CLI](../linux/spot-cli.md)或[模板](../linux/spot-template.md)创建一个专色 VM。

如果遇到错误，请参阅[错误代码](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。