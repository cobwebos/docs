---
title: Azure Site Recovery-通过使用 Azure PowerShell 在复制 Azure 虚拟机时排除磁盘 |Microsoft Docs
description: 了解如何使用 Azure PowerShell 在 Azure Site Recovery 过程中排除 Azure 虚拟机的磁盘。
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 81d22250262351e3c1bbb2fe28960b3d158bbf57
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147053"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>对 Azure VM 进行 PowerShell 复制时排除磁盘

本文介绍如何在复制 Azure VM 时排除磁盘。 可以通过排除磁盘来优化消耗的复制带宽，或者优化此类磁盘使用的目标端资源。 目前，仅通过 Azure PowerShell 提供此功能。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

开始之前：

- 请确保了解[灾难恢复体系结构和组件](azure-to-azure-architecture.md)。
- 查看所有组件的[支持要求](azure-to-azure-support-matrix.md)。
- 确保有 AzureRm PowerShell 的“Az”模块。 若要安装或更新 PowerShell，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 确保已经创建恢复服务保管库并对虚拟机进行了至少一次保护。 如果尚未做这些事，请按[使用 Azure PowerShell 为 Azure 虚拟机设置灾难恢复](azure-to-azure-powershell.md)中介绍的过程操作。

## <a name="why-exclude-disks-from-replication"></a>为什么要从复制中排除磁盘
需要从复制中排除磁盘可能是因为：

- 虚拟机已达到 [Azure Site Recovery 对数据更改复制速率的限制](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)。

- 在排除的磁盘上改动的数据不重要或不需要复制。

- 需要节省存储和网络资源，因此不复制此数据。

## <a name="how-to-exclude-disks-from-replication"></a>如何从复制中排除磁盘

在本示例中, 我们将包含一个操作系统的虚拟机和美国东部地区的三个数据磁盘复制到美国西部2区域。 虚拟机的名称为“AzureDemoVM”。 我们排除磁盘 1，保留磁盘 2 和 3。

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

获取虚拟机的磁盘的详细信息。 此信息会在以后启动 VM 复制时用到。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>复制 Azure 虚拟机

对于以下示例，我们假设你已有缓存存储帐户、复制策略和映射。 如果没有这些项目，请按[使用 Azure PowerShell 为 Azure 虚拟机设置灾难恢复](azure-to-azure-powershell.md)中介绍的过程操作。

复制包含*托管磁盘*的 Azure 虚拟机。

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

启动复制操作成功后，VM 数据将复制到恢复区域。

转到 Azure 门户即可看到复制的 VM 位于“复制的项”下。

复制过程首先在恢复区域中植入虚拟机复制磁盘的副本。 此阶段中称为初始复制阶段。

初始复制完成后，复制过程进入差异同步阶段。 此时，虚拟机受到保护。 选择受保护的虚拟机，看是否排除了任何磁盘。

## <a name="next-steps"></a>后续步骤

了解如何[运行测试性故障转移](site-recovery-test-failover-to-azure.md)。
