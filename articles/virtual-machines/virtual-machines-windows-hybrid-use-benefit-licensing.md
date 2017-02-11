---
title: "适用于 Windows Server 的 Azure 混合使用权益 | Microsoft Docs"
description: "了解如何充分利用 Windows Server 软件保障权益，以将本地许可证带入 Azure"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/13/2016
ms.author: georgem
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 5c40b318be2503fe2ec05e9f2a5a09c46b88a0dc


---
# <a name="azure-hybrid-use-benefit-for-windows-server"></a>适用于 Windows Server 的 Azure Hybrid Use Benefit
对于配合软件保证使用 Windows Server 的客户，可将本地 Windows Server 许可证带到 Azure，并以较低的成本在 Azure 中运行 Windows Server VM。 Azure Hybrid Use Benefit 可让你在 Azure 中运行 Windows Server VM，且只需支付基本计算费用。 有关详细信息，请参阅 [Azure 混合使用权益许可页](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 本文说明如何在 Azure 中部署 Windows Server VM，以利用此许可权益。

> [!NOTE]
> 如果要利用 Azure Hybrid Use Benefit，就不能使用 Azure 应用商店映像来部署 Windows Server VM。 必须使用 PowerShell 或 Resource Manager 模板部署 VM，以将 VM 正确注册为符合基本计算费率折扣资格。
>
>

## <a name="pre-requisites"></a>先决条件
若要使 Azure 中的 Windows Server VM 利用 Azure Hybrid Use Benefit，必须符合以下几项先决条件：

* 已安装 Azure PowerShell 模块
* 已将 Windows Server VHD 上载到 Azure 存储空间

### <a name="install-azure-powershell"></a>安装 Azure PowerShell
确保[已安装并配置最新的 Azure PowerShell](../powershell-install-configure.md)。 即使要使用 Resource Manager 模板部署 VM，仍需安装 Azure PowerShell 才能上载 Windows Server VHD（请参阅以下步骤）。

### <a name="upload-a-windows-server-vhd"></a>上载 Windows Server VHD
若要在 Azure 中部署 Windows Server VM，必须先创建包含基本 Windows Server 版本的 VHD。 必须先通过 Sysprep 妥善准备此 VHD，再将其上载到 Azure。 可以[阅读有关 VHD 要求和 Sysprep 进程](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)以及[针对服务器角色的 Sysprep 支持](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)的详细信息。 在运行 Sysprep 之前备份 VM。 准备好 VHD 之后，即可使用 `Add-AzureRmVhd` cmdlet 将 VHD 上载到 Azure 存储帐户，如下所示：

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server、SharePoint Server 和 Dynamics 还可以利用软件保障许可。 你仍需要准备 Windows Server 映像，方法是安装应用程序组件，并相应地提供许可证密钥，然后将磁盘映像上载到 Azure。 查看使用应用程序运行 Sysprep 的相关文档，例如[使用 Sysprep 安装 SQL Server 的注意事项](https://msdn.microsoft.com/library/ee210754.aspx)或[生成 SharePoint Server 2016 参考映像 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)。
>
>

还可以阅读有关[将 VHD 上载到 Azure 的过程](virtual-machines-windows-upload-image.md#upload-the-vhd-to-your-storage-account)的详细信息

> [!TIP]
> 本文重点介绍如何部署 Windows Server VM。 还可以使用相同方式部署 Windows 客户端 VM。 在以下示例中，请将 `Server` 相应替换为 `Client`。
>
>

## <a name="deploy-a-vm-via-powershell-quick-start"></a>通过 PowerShell 快速入门部署 VM
通过 PowerShell 部署 Windows Server VM 时，可以使用 `-LicenseType` 的附加参数。 将 VHD 上载到 Azure 之后，可以使用 `New-AzureRmVM` 创建新 VM 并指定许可类型，如下所示：

```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

可以在下面[阅读有关如何通过 PowerShell 在 Azure 中部署 VM 的更详细演练](virtual-machines-windows-hybrid-use-benefit-licensing.md#detailed-powershell-walkthrough)，或阅读有关[使用 Resource Manager 和 PowerShell 创建 Windows VM](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 的不同步骤的更具描述性说明。

## <a name="deploy-a-vm-via-resource-manager"></a>通过 Resource Manager 部署 VM
在 Resource Manager 模板中，可以指定 `licenseType` 的附加参数。 可以阅读有关[创作 Azure Resource Manager 模板](../resource-group-authoring-templates.md)的详细信息。 将 VHD 上载到 Azure 之后，请编辑 Resource Manager 模板以将许可证类型包含为计算提供程序的一部分，然后照常部署模板：

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>验证 VM 是否正在利用许可权益
通过 PowerShell 或 Resource Manager 部署方法部署 VM 之后，请使用 `Get-AzureRmVM` 验证许可证类型，如下所示：

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

输出与下面类似：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

以下 VM 在部署时未提供 Azure Hybrid Use Benefit 许可（例如直接从 Azure 库部署 VM），可看出其中的差异：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="detailed-powershell-walkthrough"></a>PowerShell 详细演练
以下面详细 PowerShell 步骤演示了 VM 的完整部署。 可以在[使用 Resource Manager 和 PowerShell 创建 Windows VM](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 中了解更多上下文，包括实际的 cmdlet 和所创建的不同组件。 可逐步执行创建资源组、存储帐户和虚拟网络，然后定义 VM 并完成创建 VM 的整个过程。

首先请安全获取凭据，并设置位置和资源组名称：

```powershell
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "myResourceGroup"
```

创建一个公共 IP：

```powershell
$publicIPName = "myPublicIP"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName `
    -Location $location -AllocationMethod "Dynamic"
```

定义子网、NIC 和 VNET：

```powershell
$subnetName = "mySubnet"
$nicName = "myNIC"
$vnetName = "myVnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location `
    -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

为 VM 命名并创建 VM 配置：

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

定义 OS：

```powershell
$computerName = "myVM"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

将 NIC 添加到 VM：

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

定义要使用的存储帐户：

```powershell
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName mystorageaccount
```

上载适当准备的 VHD，并附加到 VM 以供使用：

```powershell
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/vhd/myvhd.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage `
    -SourceImageUri $urlOfUploadedImageVhd -Windows
```

最后，创建 VM 并定义许可类型，以利用 Azure Hybrid Use Benefit：

```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Server"
```

## <a name="next-steps"></a>后续步骤
阅读有关 [Azure 混合使用权益许可](https://azure.microsoft.com/pricing/hybrid-use-benefit/)的详细信息。

了解有关[使用 Resource Manager模板](../azure-resource-manager/resource-group-overview.md)的详细信息。



<!--HONumber=Nov16_HO3-->


