---
title: Azure Site Recovery - 使用 Azure PowerShell 设置和测试 Azure 虚拟机的灾难恢复 | Microsoft Docs
description: 了解如何在 Azure PowerShell 中使用 Azure Site Recovery 为 Azure 虚拟机设置灾难恢复。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 997b358a6daf2f5450c38fcbe484a584d06bf5c4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787054"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>使用 Azure PowerShell 为 Azure 虚拟机设置灾难恢复


本文介绍如何使用 Azure PowerShell 来设置和测试 Azure 虚拟机的灾难恢复。

学习如何：

> [!div class="checklist"]
> - 创建恢复服务保管库。
> - 设置 PowerShell 会话的保管库上下文。
> - 准备保管库以开始复制 Azure 虚拟机。
> - 创建网络映射。
> - 创建要将虚拟机复制到的存储帐户。
> - 将 Azure 虚拟机复制到恢复区域以实现灾难恢复。
> - 执行测试故障转移，进行验证，然后清理测试故障转移。
> - 故障转移到恢复区域。

> [!NOTE]
> 并非所有可以通过门户使用的方案功能都能通过 Azure PowerShell 使用。 目前不支持通过 Azure PowerShell 使用的部分方案功能包括：
> - 在无需显式指定虚拟机每个磁盘的情况下指定要复制虚拟机中所有磁盘的功能。  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

开始之前：
- 请确保了解[方案体系结构和组件](azure-to-azure-architecture.md)。
- 查看所有组件的[支持要求](azure-to-azure-support-matrix.md)。
- 已将 Azure PowerShell`Az`模块。 如需进安装或升级 Azure PowerShell，请遵循此[安装和配置 Azure PowerShell 指南](/powershell/install-az-ps)。

## <a name="log-in-to-your-microsoft-azure-subscription"></a>登录到 Microsoft Azure 订阅

登录到 Azure 订阅使用 Connect AzAccount cmdlet

```azurepowershell
Connect-AzAccount
```
选择 Azure 订阅。 使用 Get AzSubscription cmdlet 有权访问的有 Azure 订阅的列表。 选择要与选择 AzSubscription cmdlet 配合使用的 Azure 订阅。

```azurepowershell
Select-AzSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>获取要复制的虚拟机的详细信息

本文中的示例将美国东部区域中的某个虚拟机复制到美国西部 2 区域并在其中进行恢复。 要复制的虚拟机是包含 OS 磁盘和单个数据磁盘的虚拟机。 本示例中使用的虚拟机名称为 AzureDemoVM。

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

获取虚拟机磁盘的详细信息。 稍后在开始复制虚拟机时，将使用磁盘详细信息。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

创建要在其中创建恢复服务保管库的资源组。

> [!IMPORTANT]
> * 恢复服务保管库和要保护的虚拟机必须位于不同的 Azure 位置。
> * 恢复服务保管库的资源组和要保护的虚拟机必须位于不同的 Azure 位置。
> * 恢复服务保管库及其所属的资源组可以位于相同的 Azure 位置。

在本文的示例中，要保护的虚拟机位于美国东部区域。 为灾难恢复选择的恢复区域为美国西部 2 区域。 恢复服务保管库及其资源组都位于恢复区域（美国西部 2）

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

创建恢复服务保管库。 以下示例在美国西部 2 区域创建名为 a2aDemoRecoveryVault 的恢复服务保管库。

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```
## <a name="set-the-vault-context"></a>设置保管库上下文

> [!TIP]
> Azure Site Recovery PowerShell 模块 （Az.RecoveryServices 模块） 附带了易于使用的大多数 cmdlet 的别名。 模块中的 cmdlet 采用*\<操作 >-**AzRecoveryServicesAsr**\<对象 >* 并具有采用以下形式的等效别名 *\<操作 >-**ASR**\<对象 >*。 本文使用 cmdlet 别名以便阅读。

设置 PowerShell 会话中使用的保管库上下文。 为此，请下载保管库设置文件，并将下载的文件导入 PowerShell 会话以设置保管库上下文。

设置后，PowerShell 会话中的后续 Azure Site Recovery 操作将在所选保管库的上下文中执行。

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>准备保管库以开始复制 Azure 虚拟机

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>创建用于表示主要（源）区域的 Site Recovery 结构对象

保管库中的结构对象表示 Azure 区域。 创建主要结构对象，以表示要在保管库中保护的虚拟机所属的 Azure 区域。 在本文的示例中，要保护的虚拟机位于美国东部区域。

- 每个区域只能创建一个结构对象。
- 如果之前已在 Azure 门户中为 VM 启用了 Site Recovery 复制，则 Site Recovery 会自动创建结构对象。 如果区域存在结构对象，则无法创建新结构对象。


在开始之前，请注意 Site Recovery 操作以异步方式执行。 启动操作时，将提交 Azure Site Recovery 作业，并返回跟踪对象的作业。 使用作业跟踪对象获得最新的状态作业 (Get-ASRJob) 和监视操作状态。

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
如果在同一个保管库中保护来自多个 Azure 区域的虚拟机，请为每个源 Azure 区域创建一个结构对象。

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>创建用于表示恢复区域的 Site Recovery 结构对象

恢复结构对象表示 Azure 恢复位置。 虚拟机将复制和恢复到（发生故障转移时）该恢复结构表示的恢复区域。 本示例使用的 Azure 恢复区域是“美国西部 2”。

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>在主要结构中创建 Site Recovery 保护容器

保护容器是用于在一个结构中分组复制项的容器。

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>在恢复结构中创建 Site Recovery 保护容器

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>创建复制策略

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>在主要保护容器与恢复保护容器之间创建保护容器映射

保护容器映射将主要保护容器映射到恢复保护容器和复制策略。 针对在保护容器对之间复制虚拟机时所用的每个复制策略各创建一个映射。

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>创建用于故障回复（故障转移后的反向复制）的保护容器映射

故障转移后，准备好将故障转移的虚拟机恢复到原始 Azure 区域时，需执行故障回复。 为了故障回复，故障转移的虚拟机将从故障转移的区域反向复制到原始区域。 反向复制时，原始区域和恢复区域的角色将会切换。 原始区域现在变成新的恢复区域，而最初的恢复区域现在会变成主要区域。 反向复制的保护容器映射表示原始和恢复区域的已切换角色。

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>创建缓存存储帐户和目标存储帐户

缓存存储帐户是复制的虚拟机所在的同一 Azure 区域中的标准存储帐户。 缓存存储帐户用于在将更改转移到 Azure 恢复区域之前，暂时保存复制更改。 可为虚拟机的不同磁盘选择（但不需要）指定不同的缓存存储帐户。

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

对于**不使用托管磁盘**的虚拟机，目标存储帐户是虚拟机磁盘复制到的恢复区域中的存储帐户。 目标存储帐户可以是标准存储帐户，也可以是高级存储帐户。 根据磁盘的数据更改率（IO 写入率）以及 Azure Site Recovery 对存储类型支持的变动限制，来选择所需的存储帐户类型。

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>创建网络映射

网络映射将主要区域中的虚拟网络映射到恢复区域中的虚拟网络。 网络映射在恢复区域中指定主要虚拟网络中的虚拟机应故障转移到的 Azure 虚拟网络。 一个 Azure 虚拟网络只能映射到恢复区域中的单个 Azure 虚拟网络。

- 在恢复区域中创建要故障转移到的 Azure 虚拟网络

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- 检索主要虚拟网络（虚拟机连接到的 VNet）
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- 在主要虚拟网络与恢复虚拟网络之间创建网络映射
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State

   ```
- 为反向复制（故障回复）创建网络映射
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>复制 Azure 虚拟机

复制包含**托管磁盘**的 Azure 虚拟机。

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1  = $vm.StorageProfile.DataDisks[0].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[0]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0]. StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

```

复制包含**非托管磁盘**的 Azure 虚拟机。

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

成功启动复制操作后，虚拟机数据将复制到恢复区域。

复制过程首先在恢复区域中初始植入虚拟机复制磁盘的副本。 此阶段中称为初始复制阶段。

初始复制完成后，复制过程将转移到差异同步阶段。 此时，虚拟机受到保护，并可以对其执行测试故障转移操作。 初始复制完成后，表示虚拟机的复制项的复制状态将转换为“受保护”状态。

获取虚拟机对应的复制保护项的详细信息，监视该虚拟机的复制状态和复制运行状况。
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>执行测试故障转移，进行验证，然后清理测试故障转移

虚拟机复制进入受保护状态后，可以针对该虚拟机（针对虚拟机的复制保护项）执行测试故障转移操作。

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

执行测试故障转移。
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

等待测试故障转移操作完成。
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```
测试故障转移作业成功完成后，可以连接到测试故障转移虚拟机，并验证测试故障转移。

对测试故障转移虚拟机完成测试后，通过启动清理测试故障转移操作来清理测试副本。 此操作会删除测试故障转移创建的虚拟机测试副本。

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>故障转移到 Azure

将虚拟机故障转移到特定的恢复点。

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

成功完成故障转移后，可以提交故障转移操作。
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-failback-to-source-region"></a>重新保护和故障回复到源区域

故障转移后，当准备好返回到原始区域，开始使用更新 AzRecoveryServicesAsrProtectionDirection cmdlet 复制保护项的反向复制。

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage accountin West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $RecoveryProtContainer -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.Id
```

重新保护完成后，可以启动反方向 （到美国东部的美国西部） 和故障回复到源区域中的故障转移。

## <a name="next-steps"></a>后续步骤
视图[Azure Site Recovery PowerShell 参考](https://docs.microsoft.com/powershell/module/az.RecoveryServices)若要了解如何执行其他任务，如创建恢复计划和测试通过 PowerShell 恢复计划的故障转移。
