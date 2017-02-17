---
title: "从经典部署模型迁移到 Azure Resource Manager 部署模型的过程中出现的常见错误 | Microsoft Docs"
description: "本文编录了从 Azure 服务管理将 IaaS 资源迁移到 Azure Resource Manager 堆栈的过程中出现的最常见错误和缓解措施。"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5bc03a1b-eb1c-438c-83d9-f0e9d61f1b6a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: daa311fcfd1ef06cf36e9443150fc967f0f39708
ms.openlocfilehash: 052505260c0998c8528146c4985400126d094f0d


---
# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>从经典部署模型迁移到 Azure Resource Manager 部署模型的过程中出现的常见错误
本文编录了从 Azure 经典部署模型将 IaaS 资源迁移到 Azure Resource Manager 堆栈的过程中出现的最常见错误和缓解措施。

## <a name="list-of-errors"></a>错误列表
| 错误字符串 | 缓解措施 |
| --- | --- |
| 内部服务器错误 |在某些情况下，这是重试时会消失的暂时性错误。 如果该错误仍然存在，请[联系 Azure 支持人员](../azure-supportability/how-to-create-azure-support-request.md)，因为它需要调查平台日志。 <br><br> **注意：**支持团队跟踪事件后，请不要尝试任何自我缓解措施，因为这可能会对你的环境造成意想不到的后果。 |
| HostedService {hosted-service-name} 中的部署 {deployment-name} 不支持迁移，因为它是 PaaS 部署（Web/辅助角色）。 |当部署包含 Web/辅助角色时，会发生这种情况。 由于只有虚拟机才支持迁移，请从部署中删除 Web/辅助角色，然后重试迁移。 |
| 模板 {template-name} 部署失败。 CorrelationId={guid} |在迁移服务的后端，我们将使用 Azure Resource Manager 模板在 Azure Resource Manager 堆栈中创建资源。 由于模板是幂等的，通常可以安全地重试迁移操作，以通过此错误。 如果此错误仍然存在，请[联系 Azure 支持人员](../azure-supportability/how-to-create-azure-support-request.md)，并向他们提供 CorrelationId。 <br><br> **注意：**支持团队跟踪事件后，请不要尝试任何自我缓解措施，因为这可能会对你的环境造成意想不到的后果。 |
| 虚拟网络 {virtual-network-name} 不存在。 |如果在新的 Azure 门户中创建虚拟网络，则可能会发生这种情况。 实际的虚拟网络名称遵循模式“Group * <VNET name>” |
| 托管服务 {hosted-service-name} 中的 VM {vm-name} 包含 Azure Resource Manager 不支持的扩展 {extension-name}。 建议从 VM 中卸载该扩展，然后再继续迁移。 |Azure Resource Manager 不支持 XML 扩展，如 BGInfo 1.*。 因此，无法迁移这些扩展。 如果将这些扩展保留安装在虚拟机上，则在完成迁移之前会自动将其卸载。 |
| 托管服务 {hosted-service-name} 中的 VM {vm-name} 包含迁移当前不支持的扩展 VMSnapshot/VMSnapshotLinux。 请从 VM 中卸载它，在迁移完成后再使用 Azure Resource Manager 重新添加它 |这是为 Azure 备份配置虚拟机的方案。 由于这是当前不支持的方案，请按照 https://aka.ms/vmbackupmigration 中的解决方法进行操作 |
| 托管服务 {hosted-service-name} 中的 VM {vm-name} 包含未从 VM 报告其状态的扩展 {extension-name}。 因此，此 VM 无法迁移。 确保从此 VM 报告该扩展状态或者将该扩展从此 VM 中卸载，然后重试迁移。 <br><br> 托管服务 {hosted-service-name} 中的 VM {vm-name} 包含报告处理程序状态: {handler-status} 的扩展 {extension-name}。 因此，此 VM 无法迁移。 确保所报告的扩展处理程序状态为 {handler-status} 或将该扩展从 VM 中卸载，然后重试迁移。 <br><br> 托管服务 {hosted-service-name} 中 VM {vm-name} 的 VM 代理正在将总体代理状态报告为“未就绪”。 因此，该 VM 无法迁移（如果它有可迁移的扩展）。 请确保 VM 代理将总体代理状态报告为“就绪”。 请参阅 https://aka.ms/classiciaasmigrationfaqs。 |Azure 来宾代理和 VM 扩展需要对 VM 存储帐户进行出站 Internet 访问以填充其状态。 状态失败的常见原因包括 <li> 阻止出站访问 Internet 的网络安全组 <li> 如果 VNET 有本地 DNS 服务器并且 DNS 连接已丢失 <br><br> 如果仍然看到不支持的状态，则可以卸载该扩展以跳过此检查并继续进行迁移。 |
| 托管服务 {hosted-service-name} 中的部署 {deployment-name} 不支持迁移，因为它具有多个可用性集。 |目前，只有具有 1 个或更少可用性集的托管服务可以进行迁移。 若要解决此问题，请将额外的可用性集及这些可用性集中的虚拟机移到其他托管服务。 |
| 托管服务 {hosted-service-name} 中的部署 {deployment-name} 不支持迁移，因为它的 VM 不属于可用性集，即使托管服务包含一个可用性集。 |这种情况的解决方法是将所有虚拟机都移到单个可用性集中，或者从托管服务的可用性集中删除所有虚拟机。 |
| 存储帐户/托管服务/虚拟网络 {virtual-network-name} 正处于迁移过程中，因此不能更改 |对资源的“准备”迁移操作已完成并触发了对资源的更改操作时，会发生此错误。 由于在“准备”操作完成后锁定了管理平面，因此将阻止对资源的任何更改。 若要解锁管理平面，可以运行“提交”迁移操作以完成迁移，或者“中止”迁移操作以回退“准备”操作。 |
| 托管服务 {hosted-service-name} 不允许迁移，因为它的 VM {vm-name} 处于状态: RoleStateUnknown。 仅当 VM 处于以下状态之一时允许迁移 -“正在运行”、“已停止”、“已停止解除分配”。 |该 VM 可能正在进行状态转换，这通常在对托管服务进行更新操作（例如，重新启动、安装扩展等）期间发生。建议等到对托管服务的更新操作完成后，再尝试迁移。 |
| HostedService {hosted-service-name} 中的部署 {deployment-name} 包含具有数据磁盘 {data-disk-name} 的 VM {vm-name}，该数据磁盘的物理 Blob 大小 {size-of-the-vhd-blob-backing-the-data-disk} 字节数不匹配 VM 数据磁盘逻辑大小 {size-of-the-data-disk-specified-in-the-vm-api} 字节数。 迁移将继续，而无需指定 Azure Resource Manager VM 的数据磁盘的大小。 如果你想要在继续迁移之前更正数据磁盘大小，请访问 https://aka.ms/vmdiskresize。 | 如果已调整 VHD Blob 的大小，而没有更新 VM API 模型中的大小，将发生此错误。 详细的缓解措施步骤如[下](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes)所述。|

## <a name="detailed-mitigations"></a>详细的缓解措施

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>其数据磁盘的物理 Blob 大小字节数不匹配 VM 数据磁盘逻辑大小字节数的 VM。

当数据磁盘的逻辑大小可以与实际的 VHD Blob 大小不同步时，将发生这种情况。 可以使用以下命令轻松地验证是否出现这种情况：

#### <a name="verifying-the-issue"></a>验证问题

```PowerShell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzureStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>缓解问题

```PowerShell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

## <a name="next-steps"></a>后续步骤
下面是介绍该过程的迁移文章列表。

* [平台支持的从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移](virtual-machines-windows-migration-classic-resource-manager.md)
* [使用 PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
* [使用 CLI 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)




<!--HONumber=Dec16_HO1-->


