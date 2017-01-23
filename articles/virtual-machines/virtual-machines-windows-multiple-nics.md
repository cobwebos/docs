---
title: "创建具有多个 NIC 的 Windows VM | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 或 Resource Manager 模板创建附有多个 NIC 的 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: d06a5c281b9390ae7d7f3a9ef18b27b62355a33f


---
# <a name="creating-a-windows-vm-with-multiple-nics"></a>创建具有多个 NIC 的 Windows VM
可以在 Azure 中创建附有多个虚拟网络接口 (NIC) 的虚拟机 (VM)。 一种常见方案是为前端和后端连接使用不同的子网，或者为监视或备份解决方案使用一个专用网络。 本文提供用于创建附有多个 NIC 的 VM 的快速命令。 有关详细信息，包括如何在自己的 PowerShell 脚本中创建多个 NIC，请阅读[部署具有多个 NIC 的 VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)。 不同的 [VM 大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)支持不同数目的 NIC，因此请相应地调整 VM 的大小。

> [!WARNING]
> 必须在创建 VM 时附加多个 NIC - 不能将 NIC 添加到现有 VM。 可以[基于原始虚拟磁盘创建 VM](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，并在部署 VM 时创建多个 NIC。
> 
> 

## <a name="create-core-resources"></a>创建核心资源
确保[已安装并配置最新的 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 登录到你的 Azure 帐户：

```powershell
Login-AzureRmAccount
```

在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 `myResourceGroup`、`mystorageaccount` 和 `myVM`。

首先创建一个资源组。 以下示例在 `WestUs` 位置创建名为 `myResourceGroup` 的资源组：

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

创建一个用于存放 VM 的存储帐户。 以下示例创建名为 `mystorageaccount` 的存储帐户：

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>创建虚拟网络和子网
定义两个虚拟网络子网 - 一个用于前端流量，一个用于后端流量。 以下示例定义两个子网，分别名为 `mySubnetFrontEnd` 和 `mySubnetBackEnd`：

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

创建虚拟网络和子网。 以下示例创建一个名为 `myVnet` 的虚拟网络：

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>创建多个 NIC
创建两个 NIC，并将其中一个 NIC 附加到前端子网，将另一个 NIC 附加到后端子网。 以下示例创建两个 NIC，分别名为 `myNic1` 和 `myNic2`：

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

通常，还会创建[网络安全组](../virtual-network/virtual-networks-nsg.md)或[负载平衡器](../load-balancer/load-balancer-overview.md)来帮助管理流量以及跨 VM 分布流量。 [更详细的多 NIC VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) 一文将指导你创建网络安全组和分配 NIC。

## <a name="create-the-virtual-machine"></a>创建虚拟机
立即开始构建 VM 配置。 每种 VM 大小限制了可添加到 VM 的 NIC 数目。 有关详细信息，请阅读 [Windows VM 大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

首先，将你的 VM 凭据设置为 `$cred` 变量，如下所示：

```powershell
$cred = Get-Credential
```

以下示例定义名为 `myVM` 的 VM，并使用最多支持两个 NIC 的 VM 大小 (`Standard_DS2_v2`)：

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

创建 VM 配置的其余部分。 以下示例创建一个 Windows Server 2012 R2 VM：

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName "myVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

附加前面创建的两个 NIC：

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

为新 VM 配置存储和虚拟磁盘：

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

最后，创建 VM：

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>使用 Resource Manager 模板创建多个 NIC
Azure Resource Manager 模板使用声明性 JSON 文件来定义环境。 可以阅读 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。 Resource Manager 模板可让你在部署期间创建资源的多个实例，例如，创建多个 NIC。 使用 *copy* 指定要创建的实例数：

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

阅读有关[使用 *copy* 创建多个实例](../resource-group-create-multiple.md)的详细信息。 

也可以使用 `copyIndex()` 并在资源名称中追加一个数字，来创建 `myNic1`、`MyNic2`，等等。下面显示了追加索引值的示例：

```json
"name": "[concat('myNic', copyIndex())]", 
```

可以阅读[使用 Resource Manager 模板创建多个 NIC](../virtual-network/virtual-network-deploy-multinic-arm-template.md) 的完整示例。

## <a name="next-steps"></a>后续步骤
尝试创建具有多个 NIC 的 VM 时，请务必查看 [Windows VM 大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 注意每个 VM 大小支持的 NIC 数目上限。 

请记住，不能将其他 NIC 添加到现有 VM，而必须在部署 VM 时创建所有 NIC。 仔细规划部署，确保从一开始就建立了全部所需的网络连接。




<!--HONumber=Dec16_HO2-->


