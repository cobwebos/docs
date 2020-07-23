---
title: 使用 Azure PowerShell 指定 Marketplace 购买计划信息
description: 了解如何在共享映像库中创建映像时指定 Azure Marketplace 购买计划详细信息。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3de79e5cb3db2d0c52d13826900ec7160271edf9
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225047"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>创建映像时提供 Azure Marketplace 购买计划信息

如果要使用最初从 Azure Marketplace 映像创建的源在共享库中创建映像，可能需要跟踪购买计划信息。 本文介绍如何查找 VM 的购买计划信息，并在创建映像定义时使用该信息。 此外，我们还介绍了如何使用映像定义中的信息，简化在创建映像的 VM 时提供购买计划信息。

有关查找和使用 Marketplace 映像的详细信息，请参阅[查找和使用 Azure Marketplace 映像](./windows/cli-ps-findimage.md)。


## <a name="get-the-source-vm-information"></a>获取源 VM 信息
如果仍有原始 VM，可以使用 New-azvm 从其获取计划、发布者和 sku 信息。 此示例在*myResourceGroup*资源组中获取名为*myVM*的 VM，并显示采购计划信息。

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan.Publisher
$vm.Plan.Name
$vm.Plan.Product
```

## <a name="create-the-image-definition"></a>创建映像定义

获取要用于存储图像的映像库。 您可以首先列出所有库。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

然后为要使用的库创建变量。 在此示例中，我们将为 `$gallery` *myGalleryRG*资源组中的*myGallery*创建一个名为的变量。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

使用 `-PurchasePlanPublisher` 、和参数创建映像定义 `-PurchasePlanProduct` `-PurchasePlanName` 。

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

然后，使用[AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)创建映像版本。 可以通过[VM](image-version-vm-powershell.md#create-an-image-version)、[托管映像](image-version-managed-image-powershell.md#create-an-image-version)、 [VHD\snapshot](image-version-snapshot-powershell.md#create-an-image-version)或[其他映像版本](image-version-another-gallery-powershell.md#create-the-image-version)来创建映像版本。 


## <a name="create-the-vm"></a>创建 VM

当你从映像创建 VM 时，可以使用映像定义中的信息，通过[AzVMPlan 将](/powershell/module/az.compute/set-azvmplan)其传入发布服务器信息。


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
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
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>后续步骤

有关查找和使用 Marketplace 映像的详细信息，请参阅[查找和使用 Azure Marketplace 映像](./windows/cli-ps-findimage.md)。