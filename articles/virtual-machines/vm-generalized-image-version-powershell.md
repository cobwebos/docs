---
title: 从通用映像创建 VM
description: 使用共享映像库中的一般化映像创建 VM。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: efce2eb33bb95a3dc13cec4cbe0ea89d1a2c3452
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796741"
---
# <a name="create-a-vm-using-a-generalized-image"></a>使用一般化映像创建 VM 

从共享映像库中存储的一般化映像创建 VM。 如果要使用专用映像创建 VM，请参阅[从专用映像创建 vm](vm-specialized-image-version-powershell.md)。

获得通用化映像版本后，可以创建一个或多个新 Vm。 使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet。 

在此示例中，我们使用映像定义 ID 来确保新 VM 使用最新版本的映像。 你还可以使用的映像版本 ID 来使用特定版本`Set-AzVMSourceImage -Id`。 例如，若要使用映像版本 *1.0.0*，请键入：`Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`。 

请注意，使用特定映像版本意味着如果特定映像版本由于已删除或已从区域中删除而无法使用，则自动化可能会失败。 建议使用映像定义 ID 创建新的 VM，除非需要特定的映像版本。

在这些示例中，请根据需要替换资源名称。 

## <a name="simplified-parameter-set"></a>简化的参数集

可以使用简化的参数集从映像快速创建 VM。 简化的参数集使用 VM 名称自动创建一些必需的资源，例如 vNet 和公共 IP 地址。 

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name $vmName `
   -Image $imageDefinition.Id
```



## <a name="full-parameter-set"></a>完整参数集

可以使用完整参数集，使用特定资源创建 VM。

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
  -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 `
   -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name myNic `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageDefinition.Id to use the latest image version.
$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```


## <a name="next-steps"></a>后续步骤
[Azure 映像生成器（预览版）](./windows/image-builder-overview.md)可帮助自动创建映像版本，甚至还可以使用它来更新[现有映像版本并创建新的映像版本](./windows/image-builder-gallery-update-image-version.md)。 

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共享映像库中创建映像定义](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [从映像版本创建 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

有关共享映像库的详细信息，请参阅[概述](./windows/shared-image-galleries.md)。 如果遇到问题，请参阅[排查共享映像库问题](./windows/troubleshooting-shared-images.md)。