---
title: "在 Azure 专用 VHD 中创建 Windows VM | Microsoft Docs"
description: "在 Resource Manager 部署模型中，通过将专用托管磁盘附加为 OS 磁盘来创建新 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: cynthn
ms.openlocfilehash: 578d31aef5ddeafbd806d0bae4231c135968f78a
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2018
---
# <a name="create-a-windows-vm-from-a-specialized-disk-using-powershell"></a>使用 PowerShell 从专用磁盘创建 Windows VM

通过将专用托管磁盘附加为 OS 磁盘来创建新 VM。 专用磁盘是保留原始 VM 中的用户帐户、应用程序和其他状态数据的现有 VM 中虚拟硬盘 (VHD) 的副本。 

使用专用 VHD 创建新 VM 时，新 VM 将保留原始 VM 的计算机名。 还会保留其他计算机特定信息，在某些情况下，这种重复信息可能会导致问题。 请注意，在复制 VM 时，应用程序依赖哪些类型的计算机特定信息。

有几种选项：
* [使用现有托管磁盘](#option-1-use-an-existing-disk)。 如果你有一个未正常工作的 VM，这很有用。 可以删除该 VM，并重用托管磁盘创建新 VM。 
* [上传 VHD](#option-2-upload-a-specialized-vhd) 
* [使用快照复制现有 Azure VM](#option-3-copy-an-existing-azure-vm)

还可以使用 Azure 门户[从专用 VHD 创建新 VM](create-vm-specialized-portal.md)。

本主题演示如何使用托管磁盘。 如果有需要使用存储帐户的旧版部署，请参阅[从存储帐户中的专用 VHD 创建 VM](sa-create-vm-specialized.md)

## <a name="before-you-begin"></a>开始之前
如果使用 PowerShell，请确保使用最新版本的 AzureRM.Compute PowerShell 模块。 

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
有关详细信息，请参阅 [Azure PowerShell 版本控制](/powershell/azure/overview)。

## <a name="option-1-use-an-existing-disk"></a>选项 1：使用现有磁盘

如果你的 VM 已删除，并想要重用 OS 磁盘创建新 VM，请使用 [Get-AzureRmDisk](/azure/powershell/get-azurermdisk)。

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzureRmDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
现在可以将此磁盘作为 OS 磁盘附加到[新 VM](#create-the-new-vm)。

## <a name="option-2-upload-a-specialized-vhd"></a>选项 2：上传专用 VHD

可从使用本地虚拟化工具（如 Hyper-V）创建的专用 VM 或从另一个云导出的 VM 上传 VHD。

### <a name="prepare-the-vm"></a>准备 VM
如果打算按原样使用 VHD 来创建新 VM，请确保完成以下步骤。 
  
  * [准备好要上传到 Azure 的 Windows VHD](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 **不要**使用 Sysprep 通用化 VM。
  * 删除 VM 上安装的所有来宾虚拟化工具和代理（例如 VMware 工具）。
  * 确保 VM 配置为通过 DHCP 提取 IP 地址和 DNS 设置。 这可以确保服务器在启动时获得 VNet 中的 IP 地址。 


### <a name="get-the-storage-account"></a>获取存储帐户
Azure 中需要有一个存储帐户用于存储上传的 VHD。 可以使用现有存储帐户，也可以创建新的存储帐户。 

若要显示可用的存储帐户，请键入：

```powershell
Get-AzureRmStorageAccount
```

如果要使用现有存储帐户，请转到[上传 VHD](#upload-the-vhd-to-your-storage-account) 部分。

如果需要创建存储帐户，请执行以下步骤：

1. 需要使用要在其中创建存储帐户的资源组的名称。 若要找出订阅中的所有资源组，请键入：
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    若要在*美国西部*区域创建一个名为 *myResourceGroup* 的资源组，请键入：

    ```powershell
    New-AzureRmResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. 使用 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet 在此资源组中创建名为 *mystorageaccount* 的存储帐户：
   
    ```powershell
    New-AzureRmStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>将 VHD 上传到存储帐户 
使用 [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) cmdlet 将 VHD 上传到存储帐户中的容器。 本示例将文件 myVHD.vhd 从 `"C:\Users\Public\Documents\Virtual hard disks\"` 上传到 myResourceGroup 资源组中名为 mystorageaccount 的存储帐户。 该文件存储在名为“mycontainer”的容器中，新文件名为“myUploadedVHD.vhd”。

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


如果成功，会显示类似于下面的响应：

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

根据网络连接速度和 VHD 文件的大小，此命令可能需要一段时间才能完成。

### <a name="create-a-managed-disk-from-the-vhd"></a>从 VHD 创建托管磁盘

使用 [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk)，从存储帐户的专用 VHD 创建托管磁盘。 此示例使用“myOSDisk1”作为磁盘名称，将磁盘置于“StandardLRS”存储中并使用 *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* 作为源 VHD 的 URI。

创建适用于新 VM 的新资源组。

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

从上传的 VHD 创建新 OS 磁盘。 

```powershell
$sourceUri = (https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd)
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType StandardLRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>选项 3：复制现有 Azure VM

通过拍摄 VM 快照来创建使用托管磁盘的 VM 副本，然后使用该快照创建一个新的托管磁盘和一个新 VM。


### <a name="take-a-snapshot-of-the-os-disk"></a>拍摄 OS 磁盘快照

可拍摄整个 VM（包括所有磁盘）快照或仅拍摄单个磁盘快照。 以下步骤展示了如何使用 [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) cmdlet 拍摄仅 VM 的 OS 磁盘的快照。 

设置一些参数。 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

获取 VM 对象。

```powershell
$vm = Get-AzureRmVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
获取 OS 磁盘名称。

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

创建快照配置。 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

拍摄快照。

```powershell
$snapShot = New-AzureRmSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


如果计划使用快照创建需要高性能的 VM，请结合使用 `-AccountType Premium_LRS` 参数和 New-AzureRmSnapshot 命令。 此参数将创建快照，以便将其存储为高级托管磁盘。 高级托管磁盘比标准托管磁盘费用高。 因此使用该参数前，请确保确实需要高级托管磁盘。

### <a name="create-a-new-disk-from-the-snapshot"></a>从快照创建新磁盘

使用 [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk) 从快照创建托管磁盘。 此示例使用“myOSDisk”作为磁盘名称。

创建适用于新 VM 的新资源组。

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

设置 OS 磁盘名称。 

```powershell
$osDiskName = 'myOsDisk'
```

创建托管磁盘。

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>创建新 VM 

创建新 VM 使用的网络和其他 VM 资源。

### <a name="create-the-subnet-and-vnet"></a>创建子网和 vNet

创建[虚拟网络](../../virtual-network/virtual-networks-overview.md)的 vNet 和子网。

创建子网。 本示例在资源组“myDestinationResourceGroup”中创建名为“mySubNet”的子网，并将子网地址前缀设置为 10.0.0.0/24。
   
```powershell
$subnetName = 'mySubNet'
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
   -Name $subnetName `
   -AddressPrefix 10.0.0.0/24
```

创建 vNet。 本示例将虚拟网络名称设置为 **myVnetName**，将位置设置为**美国西部**，将虚拟网络的地址前缀设置为 **10.0.0.0/16**。 
   
```powershell
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork `
   -Name $vnetName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $singleSubnet
```    


### <a name="create-the-network-security-group-and-an-rdp-rule"></a>创建网络安全组和 RDP 规则
若要使用 RDP 登录到 VM，需要创建一个允许在端口 3389 上进行 RDP 访问的安全规则。 由于新 VM 的 VHD 是从现有专用 VM 创建的，因此可以将源虚拟机中的帐户用于 RDP。

本示例将 NSG 名称设置为 **myNsg**，将 RDP 规则名称设置为 **myRdpRule**。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

有关终结点和 NSG 规则的详细信息，请参阅 [Opening ports to a VM in Azure using PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（使用 PowerShell 在 Azure 中打开 VM 端口）。

### <a name="create-a-public-ip-address-and-nic"></a>创建公共 IP 地址和 NIC
若要与虚拟网络中的虚拟机通信，需要一个 [公共 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和网络接口。

创建公共 IP 地址。 在本示例中，公共 IP 地址名称设置为 **myIP**。
   
```powershell
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress `
   -Name $ipName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AllocationMethod Dynamic
```       

创建 NIC。 在本示例中，NIC 名称设置为 **myNicName**。
   
```powershell
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location -SubnetId $vnet.Subnets[0].Id `
   -PublicIpAddressId $pip.Id `
   -NetworkSecurityGroupId $nsg.Id
```



### <a name="set-the-vm-name-and-size"></a>设置 VM 名称和大小

此示例将 VM 名称设置为“myVM”，将 VM 大小设置为“Standard_A2”。

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>添加 NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>添加 OS 磁盘 

使用 [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) 向配置添加 OS 磁盘。 此示例将磁盘大小设置为 *128 GB* 并附加托管磁盘作为 *Windows* OS 磁盘。
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>完成 VM 

使用刚刚创建的 [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) 配置创建 VM。

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

如果此命令成功，会看到类似于下面的输出：

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>验证是否已创建 VM
应会在 [Azure 门户](https://portal.azure.com)的“浏览” > “虚拟机”下看到新建的 VM，也可以使用以下 PowerShell 命令查看该 VM：

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>后续步骤
要登录到新虚拟机，请在[门户](https://portal.azure.com)中浏览到该 VM，单击“连接”，并打开远程桌面 RDP 文件。 使用原始虚拟机的帐户凭据登录到新虚拟机。 有关详细信息，请参阅 [How to connect and log on to an Azure virtual machine running Windows](connect-logon.md)（如何连接并登录到运行 Windows 的 Azure 虚拟机）。

