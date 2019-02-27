---
title: Azure Site Recovery - 在复制 Azure 虚拟机时使用 Azure PowerShell 排除磁盘 | Microsoft Docs
description: 了解如何通过 Azure Site Recovery 使用 Azure PowerShell 为 Azure 虚拟机排除磁盘。
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 978566eb9e0073c60046eca024e09ba63c642180
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458224"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>将磁盘从 Azure VM 复制到 Azure 时使用 Azure PowerShell 排除磁盘

本文介绍如何在复制 Azure VM 时排除磁盘。 这种排除可以优化消耗的复制带宽，或者优化此类磁盘利用的目标端资源。 目前，仅通过 Azure PowerShell 公开此功能。

## <a name="prerequisites"></a>先决条件

开始之前：

- 请确保了解[方案体系结构和组件](azure-to-azure-architecture.md)。
- 查看所有组件的[支持要求](azure-to-azure-support-matrix.md)。
- 拥有 5.7.0 或更高版本的 AzureRm PowerShell 模块。 如需进安装或升级 Azure PowerShell，请遵循此[安装和配置 Azure PowerShell 指南](/powershell/azureps-cmdlets-docs)。
- 已创建恢复服务保管库，并且至少已对虚拟机进行了一次保护。 如果没有，请使用[此处](azure-to-azure-powershell.md)提到的文档进行操作 

## <a name="why-exclude-disks-from-replication"></a>为何要从复制中排除磁盘？
从复制中排除磁盘通常因以下原因而有必要：

- 虚拟机已达到 [Azure Site Recovery 对数据更改复制速率的限制](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#azure-site-recovery-limits-to-replicate-data-change-rates)

- 排除的磁盘上改动的数据不重要或不需要复制。

- 用户需要节省存储和网络资源，因此不复制此改动。


## <a name="how-to-exclude-disks-from-replication"></a>如何从复制中排除磁盘？

在本文的示例中，美国东部地区拥有 1 个 OS 和 3 个数据磁盘的虚拟机将被复制到美国西部 2 区域。 本示例中使用的虚拟机名称为 AzureDemoVM。 并且我们将排除磁盘 1 并保留磁盘 2 和 3

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>获取要复制的虚拟机的详细信息

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

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


获取虚拟机磁盘的详细信息。 稍后在开始复制虚拟机时，将使用磁盘详细信息。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>复制 Azure 虚拟机

在下面的示例中，我们假设已有缓存存储帐户、复制策略和映射。 如果没有，请使用[此处](azure-to-azure-powershell.md)提到的文档进行操作 


复制包含**托管磁盘**的 Azure 虚拟机。

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

成功启动复制操作后，虚拟机数据将复制到恢复区域。

你可以转到 Azure 门户，在复制的项下方，可以看到正在复制的虚拟机。
复制过程首先在恢复区域中初始植入虚拟机复制磁盘的副本。 此阶段中称为初始复制阶段。

初始复制完成后，复制过程将转移到差异同步阶段。 此时，虚拟机受到保护。 单击受保护的虚拟机 > 磁盘以查看是否排除了磁盘。

## <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。