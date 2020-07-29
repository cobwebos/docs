---
title: 复制邻近放置组中运行的 Azure VM
description: 了解如何使用 Azure Site Recovery 复制邻近放置组中运行的 Azure VM。
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: 1f64c7aa45b748bdb8174bd69dbfc25f43329c10
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285333"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>将邻近放置组中运行的 Azure 虚拟机复制到另一个区域

本文介绍如何将邻近放置组中运行的虚拟机复制、故障转移和故障恢复到次要区域。

[邻近放置组](../virtual-machines/windows/proximity-placement-groups-portal.md)是一种 Azure 虚拟机逻辑分组功能，可用于降低与应用程序关联的 VM 间网络延迟。 当 VM 部署在相同的邻近放置组中时，它们彼此的物理位置会尽可能接近。 邻近放置组尤其有助于满足对延迟敏感的工作负载的要求。

## <a name="disaster-recovery-with-proximity-placement-groups"></a>邻近放置组中的灾难恢复

在典型场景中，你可能会在邻近放置组中运行虚拟机，以避免在应用程序的各个层之间出现网络延迟。 虽然这样可以最大程度减少应用程序的网络延迟，但建议使用 Site Recovery 保护这些应用程序免受任何区域级别故障。 发生故障转移时，Site Recovery 将数据从一个 Azure 区域复制到另一个 Azure 区域，并调出灾难恢复区域中的计算机。

## <a name="considerations"></a>注意事项

- 最佳做法是将虚拟机故障转移/故障恢复到邻近放置组。 但是，如果 VM 在故障转移/故障恢复期间无法在邻近放置组中调出，则故障转移/故障恢复仍将发生，并且系统将在邻近放置组之外创建虚拟机。
-  如果将某个可用性集固定到邻近放置组，且可用性集中的 VM 在故障转移/故障恢复时具有分配约束，则系统将在可用性集和邻近放置组之外创建虚拟机。
-  非托管磁盘不支持适用于邻近放置组的 Site Recovery。

> [!NOTE]
> Azure Site Recovery 不支持从适用于 Hyper-v 到 Azure 的托管磁盘的故障回复。 因此，不支持从 Azure 到 Hyper-v 的邻近位置组故障回复。

## <a name="prerequisites"></a>先决条件

1. 确保已有 Azure PowerShell Az 模块。 如需进安装或升级 Azure PowerShell，请遵循此[安装和配置 Azure PowerShell 指南](/powershell/azure/install-az-ps)。
2. 最小 Azure PowerShell Az 版本应为4.1.0。 若要检查当前版本，请使用以下命令-
    ```
    Get-InstalledModule -Name Az
    ```

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>为邻近放置组中的虚拟机设置 Site Recovery

> [!NOTE]
> 请确保你具有目标邻近组的唯一 ID。 如果要创建新的邻近位置组，请检查[此处](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#create-a-proximity-placement-group)的命令，如果使用现有的邻近位置组，请在[此处](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#list-proximity-placement-groups)使用命令。

### <a name="azure-to-azure"></a>Azure 到 Azure

1. [登录](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription)到 Azure 帐户并设置订阅。
2. 按[此处](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated)所述获取你计划复制的虚拟机的详细信息。
3. [创建](./azure-to-azure-powershell.md#create-a-recovery-services-vault)恢复服务保管库并[设置](./azure-to-azure-powershell.md#set-the-vault-context)保管库上下文。
4. 准备保管库以开始复制虚拟机。 这涉及到为主区域和恢复区域创建 [service fabric 对象](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region)。
5. 为主结构和恢复结构[创建](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) Site Recovery 保护容器。
6. [创建](./azure-to-azure-powershell.md#create-a-replication-policy)复制策略。
7. 按照[此处](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)所述，使用[这些](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container)步骤创建主保护容器和恢复保护容器之间的保护容器映射，以及用于故障恢复的保护容器映射。
8. 通过[这些](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account)步骤创建缓存存储帐户。
9. 按照[此处](./azure-to-azure-powershell.md#create-network-mappings)所述，创建所需的网络映射。
10. 若要复制包含托管磁盘的 Azure 虚拟机，请使用以下 PowerShell cmdlet-

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```
成功启动复制操作后，虚拟机数据将复制到恢复区域。

复制过程首先在恢复区域中初始植入虚拟机复制磁盘的副本。 此阶段中称为初始复制阶段。

初始复制完成后，复制过程将转移到差异同步阶段。 此时，虚拟机受到保护，可对其执行测试故障转移操作。 初始复制完成后，表示虚拟机的复制项的复制状态将转换为“受保护”状态。

获取虚拟机对应的复制保护项的详细信息，监视该虚拟机的复制状态和复制运行状况。

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. 要执行测试故障转移、进行验证，然后清理测试故障转移，请按照[这些](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover)步骤操作。
12. 要进行故障转移，请按照[此处](./azure-to-azure-powershell.md#fail-over-to-azure)所述步骤操作。
13. 要进行重新保护并故障恢复到源区域，请使用下面的 PowerShell cmdlet -

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```
14. 要禁用复制，请按照[此处](./azure-to-azure-powershell.md#disable-replication)步骤操作。

### <a name="vmware-to-azure"></a>VMware 到 Azure

1. 确保[准备好本地 VMware 服务器](./vmware-azure-tutorial-prepare-on-premises.md)，以便能够灾难恢复到 Azure。
2. 登录到你的帐户，并按照[此处](./vmware-azure-disaster-recovery-powershell.md#log-into-azure)的规定设置订阅。
3. [设置](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault)恢复服务保管库并[设置保管库上下文](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context)。
4. [验证](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration)保管库注册。
5. [创建](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy)复制策略。
6. [添加](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms) vCenter 服务器并发现虚拟机，然后[创建](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication)存储帐户以进行复制。
7. 要复制 VMware 虚拟机，请在此处查看详细信息，并遵循以下 PowerShell cmdlet -

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```
8. 可使用 Get-ASRReplicationProtectedItem cmdlet 检查虚拟机的复制状态和复制运行状况。

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
9. 按照 [此处](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings)的步骤配置故障转移设置。
10. [运行](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover)测试故障转移。 
11. 使用[这些](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure)步骤故障转移到 Azure。

### <a name="hyper-v-to-azure"></a>Hyper-V 到 Azure

1. 确保[准备好本地 Hyper-V 服务器](./hyper-v-prepare-on-premises-tutorial.md)，以便能够灾难恢复到 Azure。
2. [登录](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account)到 Azure。
3. [设置](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault)保管库并[设置](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context)恢复服务保管库上下文。
4. [创建](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site) Hyper-V 站点。
5. [安装](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent)提供程序和代理。
6. [创建](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy)复制策略。
7. 使用以下步骤启用复制 - 
    
    a. 检索与要保护的 VM 相对应的可保护项，如下所示：

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. 保护 VM。 如果要保护的 VM 有多个附加磁盘，则需使用 OSDiskName 参数指定操作系统磁盘。
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    c. 等待 VM 在完成初始复制后进入受保护状态。 这可能需要一段时间，具体取决于诸如要复制的数据量和 Azure 的可用上游带宽等因素。 进入受保护状态后，更新作业状态和 StateDescription，如下所示： 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. 更新各种恢复属性（例如 VM 角色大小），以及进行故障转移后需要将 VM NIC 连接到的 Azure 网络。

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. 运行测试[故障转移](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover)。


## <a name="next-steps"></a>后续步骤

要执行重新保护以及 VMware 到 Azure 的故障恢复，请按照[此处](./vmware-azure-prepare-failback.md)所述步骤进行操作。

要执行 Hyper-V 到 Azure 的故障转移，请按照[此处](./site-recovery-failover.md)所述步骤操作；要执行故障恢复，请按照[此处](./hyper-v-azure-failback.md)所述步骤操作。

有关详细信息，请参阅 [Site Recovery 中的故障转移](site-recovery-failover.md)。
