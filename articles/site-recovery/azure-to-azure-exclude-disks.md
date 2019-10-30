---
title: Azure Site Recovery-通过使用 Azure PowerShell 在复制 Azure 虚拟机时排除磁盘 |Microsoft Docs
description: 了解如何使用 Azure PowerShell 在 Azure Site Recovery 时排除 Azure 虚拟机的磁盘。
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 317a8ba48851ebf65fe52e79f6cf9d9c45786f6f
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053408"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>从 Azure Vm 的 PowerShell 复制中排除磁盘

本文介绍了在复制 Azure Vm 时如何排除磁盘。 你可以排除磁盘以优化使用的复制带宽或这些磁盘使用的目标端资源。 目前，此功能只能通过 Azure PowerShell 提供。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

开始之前：

- 请确保了解[灾难恢复体系结构和组件](azure-to-azure-architecture.md)。
- 查看所有组件的[支持要求](azure-to-azure-support-matrix.md)。
- 确保已 AzureRm PowerShell "Az" 模块。 若要安装或更新 PowerShell，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 请确保已创建至少一个恢复服务保管库和受保护的虚拟机。 如果尚未执行这些操作，请按照[使用 Azure PowerShell 为 Azure 虚拟机设置灾难恢复](azure-to-azure-powershell.md)中的过程进行操作。
- 如果正在寻找有关将磁盘添加到为复制启用的 Azure VM 的信息，请[参阅此文](azure-to-azure-enable-replication-added-disk.md)。

## <a name="why-exclude-disks-from-replication"></a>为什么从复制中排除磁盘
你可能需要从复制中排除磁盘，因为：

- 虚拟机已达到[复制数据更改率 Azure Site Recovery 限制](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)。

- 在排除的磁盘上改动的数据不重要或不需要复制。

- 您希望通过不复制数据来保存存储和网络资源。

## <a name="how-to-exclude-disks-from-replication"></a>如何从复制中排除磁盘

在本示例中，我们将包含一个操作系统的虚拟机和美国东部地区的三个数据磁盘复制到美国西部2区域。 虚拟机的名称为*AzureDemoVM*。 我们排除磁盘1并保留磁盘2和3。

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>获取要复制的虚拟机的详细信息

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

获取有关虚拟机磁盘的详细信息。 以后开始复制 VM 时，将使用此信息。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>复制 Azure 虚拟机

在下面的示例中，我们假设你已有一个缓存存储帐户、复制策略和映射。 如果没有这些操作，请按照[使用 Azure PowerShell 为 Azure 虚拟机设置灾难恢复](azure-to-azure-powershell.md)中的过程进行操作。

使用*托管磁盘*复制 Azure 虚拟机。

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication. 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

开始复制操作成功后，VM 数据将复制到恢复区域。

可以访问 Azure 门户，并在 "复制的项" 下查看复制的 Vm。

复制过程首先会播种恢复区域中虚拟机复制磁盘的副本。 此阶段称为初始复制阶段。

初始复制完成后，复制转到差异同步阶段。 此时，虚拟机受到保护。 选择受保护的虚拟机，查看是否已排除任何磁盘。

## <a name="next-steps"></a>后续步骤

了解如何[运行测试故障转移](site-recovery-test-failover-to-azure.md)。
