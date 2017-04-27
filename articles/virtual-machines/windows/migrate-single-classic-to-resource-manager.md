---
title: "将经典 VM 迁移到 ARM 托管磁盘 VM |Microsoft Docs"
description: "将单个 Azure VM 从经典部署模型迁移到 Resource Manager 部署模型中的托管磁盘。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 3a3730821b88062fdccf18732630be0bcb6ae7a7
ms.lasthandoff: 04/07/2017


---

# <a name="manually-migrate-a-classic-vm-to-a-new-arm-managed-disk-vm-from-the-vhd"></a>手动将经典 VM 从 VHD 迁移到新的 ARM 托管磁盘 VM 


本部分有助于将现有 Azure VM 从经典部署模型迁移到 Resource Manager 部署模型中的[托管磁盘](../../storage/storage-managed-disks-overview.md)。


## <a name="plan-for-the-migration-to-managed-disks"></a>计划迁移到托管磁盘

本部分有助于你在 VM 和磁盘类型方面做出最佳决策。


### <a name="location"></a>位置

选择 Azure 托管磁盘的可用位置。 如果要迁移到高级托管磁盘，还请确保高级存储在计划迁移到的目标区域中可用。 有关可用位置的最新信息，请参阅 [Azure 服务（按区域）](https://azure.microsoft.com/regions/#services)。

### <a name="vm-sizes"></a>VM 大小

如果要迁移到高级托管磁盘，必须将 VM 的大小更新为该 VM 所在区域中提供的支持高级存储的大小。 查看支持高级存储的 VM 大小。 [虚拟机大小](sizes.md)中列出了 Azure VM 大小规范。
查看适用于高级存储的虚拟机的性能特征并选择最适合工作负荷的 VM 大小。 确保 VM 上有足够的带宽来驱动磁盘通信。

### <a name="disk-sizes"></a>磁盘大小

**高级托管磁盘**

有三种类型的高级托管磁盘可用于 VM，每种磁盘都具有特定的 IOPS 和吞吐量限制。 根据应用程序在容量、性能、可伸缩性和峰值负载方面的需要为 VM 选择高级磁盘类型时，需要考虑这些限制。

| 高级磁盘类型  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| 磁盘大小           | 128 GB            | 512 GB            | 1024 GB (1 TB)    |
| 每个磁盘的 IOPS       | 500               | 2300              | 5000              |
| 每个磁盘的吞吐量 | 每秒 100 MB | 每秒 150 MB | 每秒 200 MB |

**标准托管磁盘**

有五种类型的标准托管磁盘可用于 VM。 每种类型的磁盘具有不同的容量，但具有相同的 IOPS 和吞吐量限制。 根据应用程序的容量需求选择标准托管磁盘的类型。

| 标准磁盘类型  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| 磁盘大小           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   |
| 每个磁盘的 IOPS       | 500              | 500              | 500              | 500              | 500              |
| 每个磁盘的吞吐量 | 每秒 60 MB | 每秒 60 MB | 每秒 60 MB | 每秒 60 MB | 每秒 60 MB |

### <a name="disk-caching-policy"></a>磁盘缓存策略 

**高级托管磁盘**

默认情况下，所有高级数据磁盘的磁盘缓存策略都是“只读”，所有附加到 VM 的高级操作系统都是“读写”。 为使应用程序的 IO 达到最佳性能，建议使用此配置设置。 对于频繁写入或只写的磁盘（例如 SQL Server 日志文件），禁用磁盘缓存可获得更佳的应用程序性能。

### <a name="pricing"></a>定价

查看[托管磁盘定价](https://azure.microsoft.com/en-us/pricing/details/managed-disks/)。 高级托管磁盘的定价与高级非托管磁盘相同。 但标准托管磁盘的定价与标准非托管磁盘不同。


## <a name="checklist"></a>清单

1.  如果要迁移到高级托管磁盘，请确保它在要迁移到的区域中可用。

2.  决定要使用的新 VM 系列。 如果要迁移到高级托管磁盘，则应支持高级存储。

3.  确定将使用的确切 VM 大小，将迁移到的区域应支持此大小。 VM 大小需要足够大以支持所拥有的数据磁盘数。 例如，如果有四个数据磁盘，则 VM 必须至少有两个内核。 此外，还应考虑处理能力、内存和网络带宽需求。

4.  手边具备当前 VM 详细信息，包括磁盘和对应的 VHD blob 的列表。

让应用程序做好停机准备。 为了执行净迁移，必须停止当前系统中的所有处理。 只有这样才能使其处于一致状态，可以将该状态迁移到新的平台。 停机持续时间取决于要迁移的磁盘中的数据量。


## <a name="migrate-the-vm"></a>迁移 VM

让应用程序做好停机准备。 为了执行净迁移，必须停止当前系统中的所有处理。 只有这样才能使其处于一致状态，可以将该状态迁移到新的平台。 停机持续时间取决于要迁移的磁盘中的数据量。


1.  首先，设置公共参数：

    ```powershell
    $resourceGroupName = 'yourResourceGroupName'
    
    $location = 'your location' 
    
    $virtualNetworkName = 'yourExistingVirtualNetworkName'
    
    $virtualMachineName = 'yourVMName'
    
    $virtualMachineSize = 'Standard_DS3'
    
    $adminUserName = "youradminusername"
    
    $adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force
    
    $imageName = 'yourImageName'
    
    $osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
    
    $dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'
    
    $dataDiskName = 'dataDisk1'
    ```

2.  使用经典 VM 中的 VHD 创建托管 OS 磁盘。

    确保已为 $osVhdUri 参数提供 OS VHD 的完整 URI。 此外，根据要迁移到的磁盘类型（高级或标准），输入 **-AccountType** 作为 **PremiumLRS** 或 **StandardLRS**。

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $osVhdUri) '
    -ResourceGroupName $resourceGroupName
    ```

3.  将 OS 磁盘附加到新的 VM。

    ```powershell
    $VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $osDisk.Id '
    -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

4.  从数据 VHD 文件中创建托管数据磁盘，并将其添加到新的 VM。

    ```powershell
    $dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreationDataCreateOption Import '
    -SourceUri $dataVhdUri ) -ResourceGroupName $resourceGroupName
    
    $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName '
    -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
    ```

5.  通过设置公共 IP、虚拟网络和 NIC 创建新的 VM。

    ```powershell
    $publicIp = New-AzureRmPublicIpAddress -Name ($VirtualMachineName.ToLower()+'_ip') '
    -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
    
    $vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName
    
    $nic = New-AzureRmNetworkInterface -Name ($VirtualMachineName.ToLower()+'_nic') '
    -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id '
    -PublicIpAddressId $publicIp.Id
    
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id
    
    New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
    ```

> [!NOTE]
>本指南可能未涵盖支持应用程序所要执行的其他步骤。
>
>

## <a name="next-steps"></a>后续步骤

- 连接到虚拟机。 有关说明，请参阅[如何连接并登录到运行 Windows 的 Azure 虚拟机](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


