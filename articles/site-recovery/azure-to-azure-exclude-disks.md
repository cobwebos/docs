---
title: Azure 站点恢复-使用 Azure PowerShell 的 Azure 虚拟机的复制期间排除磁盘 |Microsoft Docs
description: 了解如何使用 Azure PowerShell 在 Azure 站点恢复过程中排除磁盘的 Azure 虚拟机。
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678269"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>从 PowerShell 的 Azure Vm 的复制中排除磁盘

本文介绍如何排除磁盘，Azure Vm 复制时。 你可以排除磁盘，以优化消耗的复制带宽或这些磁盘使用的目标端资源。 目前，此功能目前仅通过 Azure PowerShell。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

开始之前：

- 请确保您了解[灾难恢复体系结构和组件](azure-to-azure-architecture.md)。
- 查看所有组件的[支持要求](azure-to-azure-support-matrix.md)。
- 请确保您具有"Az"的 AzureRm PowerShell 模块。 若要安装或更新 PowerShell，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 请确保你已创建恢复服务保管库并受保护的虚拟机至少一次。 如果尚未执行这些操作，请按照在进程[设置为使用 Azure PowerShell 的 Azure 虚拟机的灾难恢复](azure-to-azure-powershell.md)。

## <a name="why-exclude-disks-from-replication"></a>为什么从复制中排除磁盘
您可能需要从复制中排除磁盘，因为：

- 已达到你的虚拟机[Azure Site Recovery 限制复制的数据更改率](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)。

- 排除的磁盘改动的数据并不重要或不需要复制。

- 你想要保存，因此不复制数据的存储和网络资源。

## <a name="how-to-exclude-disks-from-replication"></a>如何从复制中排除磁盘

在本示例中，我们将复制具有一个操作系统的虚拟机和美国东部区域为美国西部 2 区域中的三个数据磁盘。 虚拟机的名称是*AzureDemoVM*。 我们排除磁盘 1 并保留磁盘 2 和 3。

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

获取有关虚拟机的磁盘的详细信息。 当您开始复制的 VM，将更高版本使用此信息。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>复制 Azure 虚拟机

下面的示例中，我们假定您已经有一个缓存存储帐户、 复制策略和映射。 如果没有这些内容，请按照在进程[设置为使用 Azure PowerShell 的 Azure 虚拟机的灾难恢复](azure-to-azure-powershell.md)。

复制 Azure 虚拟机*托管磁盘*。

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

开始复制操作成功后，VM 数据复制到恢复区域中。

可以转到 Azure 门户，请参阅复制的 Vm，在"复制的项"。

复制过程首先会种子设定在恢复区域中的虚拟机的复制磁盘的副本。 此阶段调用的初始复制阶段。

初始复制完成后，复制移动到差异同步阶段。 此时，虚拟机受到保护。 选择受保护的虚拟机，如果排除的任何磁盘。

## <a name="next-steps"></a>后续步骤

了解如何[运行测试故障转移](site-recovery-test-failover-to-azure.md)。
