---
title: 使用 Azure 中的专用 VHD 创建 Windows VM
description: 使用资源管理器部署模型，通过将专用托管磁盘附加为 OS 磁盘来创建新的 Windows VM。
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: ac18056f9bfdf22c55b5effac810b8c24ab4d81d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033856"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>使用 PowerShell 从专用磁盘创建 Windows VM

通过将专用托管磁盘附加为 OS 磁盘来创建新 VM。 专用磁盘是包含原始 VM 中的用户帐户、应用程序和其他状态数据的现有 VM 中虚拟硬盘 (VHD) 的副本。 

使用专用 VHD 创建新 VM 时，新 VM 将保留原始 VM 的计算机名。 还会保留其他计算机特定信息，在某些情况下，这种重复信息可能会导致问题。 复制 VM 时，请注意应用程序依赖哪些类型的计算机特定信息。

有几种选项：
* [使用现有托管磁盘](#option-1-use-an-existing-disk)。 如果你有一个未正常工作的 VM，此选项很有用。 可以删除该 VM，然后重用托管磁盘创建新 VM。 
* 上传 VHD 
* [使用快照复制现有 Azure VM](#option-3-copy-an-existing-azure-vm)

还可以使用 Azure 门户[从专用 VHD 创建新 VM](create-vm-specialized-portal.md)。

本文介绍如何使用托管磁盘。 如果有需要使用存储帐户的旧版部署，请参阅[从存储帐户中的专用 VHD 创建 VM](sa-create-vm-specialized.md)。

建议使用单个 VHD 或快照将并发部署数限制为20个 Vm。 

## <a name="option-1-use-an-existing-disk"></a>选项 1：使用现有磁盘

如果你的 VM 已删除，并且你希望重复使用 OS 磁盘创建新 VM，请使用 [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)。

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
现在可以将此磁盘作为 OS 磁盘附加到[新 VM](#create-the-new-vm)。

## <a name="option-2-upload-a-specialized-vhd"></a>选项 2：上传专用 VHD

可从使用本地虚拟化工具（如 Hyper-V）创建的专用 VM 或从另一个云导出的 VM 上传 VHD。

### <a name="prepare-the-vm"></a>准备 VM
使用原始 VHD 创建新的 VM。 
  
  * 准备好要上传到 Azure 的 Windows VHD。 **不要**使用 Sysprep 通用化 VM。
  * 删除 VM 上安装的所有来宾虚拟化工具和代理（例如 VMware 工具）。
  * 确保 VM 配置为从 DHCP 获取 IP 地址和 DNS 设置。 这可以确保服务器在启动时获得虚拟网络中的 IP 地址。 


### <a name="upload-the-vhd"></a>上传 VHD

你现在可以将 VHD 直接上传到托管磁盘。 有关说明，请参阅[使用 Azure PowerShell 将 VHD 上传到 Azure](disks-upload-vhd-to-managed-disk-powershell.md)。

## <a name="option-3-copy-an-existing-azure-vm"></a>选项 3：复制现有 Azure VM

通过拍摄 VM 快照来创建使用托管磁盘的 VM 副本，然后使用该快照创建一个新的托管磁盘和一个新 VM。

如果要将现有 VM 复制到其他区域，你可能想要使用 azcopy[在另一区域中的磁盘副本](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)。 

### <a name="take-a-snapshot-of-the-os-disk"></a>拍摄 OS 磁盘快照

可创建整个 VM（包括所有磁盘）的快照或仅创建单个磁盘的快照。 以下步骤说明了如何使用 [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) cmdlet 仅创建 VM OS 磁盘的快照。 

首先设置一些参数。 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

获取 VM 对象。

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
获取 OS 磁盘名称。

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

创建快照配置。 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

拍摄快照。

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


若要使用快照创建高性能的 VM，请将 `-AccountType Premium_LRS` 参数添加到 New-AzSnapshotConfig 命令。 此参数将创建快照，以便将其存储为高级托管磁盘。 高级托管磁盘的费用比标准托管磁盘更高，因此，在使用该参数之前，请确认需要高级托管磁盘。

### <a name="create-a-new-disk-from-the-snapshot"></a>从快照创建新磁盘

使用 [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk) 基于快照创建托管磁盘。 此示例使用“myOSDisk”作为磁盘名称。

创建适用于新 VM 的新资源组。

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

设置 OS 磁盘名称。 

```powershell
$osDiskName = 'myOsDisk'
```

创建托管磁盘。

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>创建新 VM 

创建新 VM 使用的网络和其他 VM 资源。

### <a name="create-the-subnet-and-virtual-network"></a>创建子网和虚拟网络

为 VM 创建[虚拟网络](../../virtual-network/virtual-networks-overview.md)和子网。

1. 创建子网。 本示例在资源组“myDestinationResourceGroup”中创建名为“mySubNet”的子网，并将子网地址前缀设置为 10.0.0.0/24。
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. 创建虚拟网络。 本示例将虚拟网络名称设置为 *myVnetName*，将位置设置为*美国西部*，将虚拟网络的地址前缀设置为 *10.0.0.0/16*。 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>创建网络安全组和 RDP 规则
若要使用远程桌面协议 (RDP) 登录到 VM，需要创建一个允许在端口 3389 上进行 RDP 访问的安全规则。 在本示例中，由于新 VM 的 VHD 是从现有专用 VM 创建的，因此，可将源虚拟机中的帐户用于 RDP。

本示例将网络安全组 (NSG) 名称设置为 *myNsg*，将 RDP 规则名称设置为 *myRdpRule*。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

有关终结点和 NSG 规则的详细信息，请参阅[使用 PowerShell 在 Azure 中打开 VM 端口](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

### <a name="create-a-public-ip-address-and-nic"></a>创建公共 IP 地址和 NIC
若要与虚拟网络中的虚拟机通信，需要一个 [公共 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)和网络接口。

1. 创建公共 IP 地址。 在本示例中，公共 IP 地址名称设置为 *myIP*。
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. 创建 NIC。 在此示例中，NIC 名称设置为 *myNicName*。
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>设置 VM 名称和大小

此示例将 VM 名称设置为“myVM”，将 VM 大小设置为“Standard_A2”。

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>添加 NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>添加 OS 磁盘 

使用 [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) 向配置中添加 OS 磁盘。 此示例将磁盘大小设置为 *128 GB* 并附加托管磁盘作为 *Windows* OS 磁盘。
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>完成 VM 

使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 以及刚才创建的配置创建 VM。

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

如果此命令成功，则会显示类似于下面的输出：

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>验证是否已创建 VM
应会在 [Azure 门户](https://portal.azure.com)的“浏览” **“虚拟机”下看到新建的 VM，也可以使用以下 PowerShell 命令查看该 VM。**  > 

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>后续步骤
登录新虚拟机。 有关详细信息，请参阅 [How to connect and log on to an Azure virtual machine running Windows](connect-logon.md)（如何连接并登录到运行 Windows 的 Azure 虚拟机）。

