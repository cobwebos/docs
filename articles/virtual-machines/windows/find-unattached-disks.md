---
title: "查找并删除未连接的 Azure 托管和非托管磁盘 | Microsoft Docs"
description: "如何使用 Azure PowerShell 查找并删除未连接的 Azure 托管和非托管（VHD/页 blob）磁盘。"
services: virtual-machines-windows
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 15c2550472156d5c1f680af77df2fe771edf3444
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>查找并删除未连接的 Azure 托管和非托管磁盘
删除 Azure 中的虚拟机 (VM) 时，默认不删除附加到 VM 的任何磁盘。 此功能可帮助防止意外删除 VM 而导致的数据丢失。 删除 VM 后，可继续支付未附加的磁盘。 本文演示了如何查找并删除任何未附加的磁盘，以及如何减少不必要的成本。 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>托管磁盘：查找并删除未附加的磁盘 

以下脚本通过检查 ManagedBy 属性的值查找未附加的[托管磁盘](managed-disks-overview.md)。 有托管磁盘附加到 VM 时，ManagedBy 属性包含 VM 的资源 ID。 未附加托管磁盘时，ManagedBy 属性为 null。 该脚本检查 Azure 订阅中的所有托管磁盘。 当脚本找到一个 ManagedBy 属性设置为 null 的托管磁盘时，脚本将确定该磁盘为未附加。

>[!IMPORTANT]
>首先，通过将 deleteUnattachedDisks 变量设置为 0 来运行脚本。 通过此操作可查找并查看所有未附加的托管磁盘。
>
>在检查所有未附加磁盘后，再次运行脚本并将 deleteUnattachedDisks 变量设置为 1。 通过此操作可删除所有未附加的托管磁盘。
>

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>非托管磁盘：查找并删除未附加的磁盘 

非托管磁盘是指以[页 blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) 形式存储在 [Azure 存储帐户](../../storage/common/storage-create-storage-account.md)中的 VHD 文件。 以下脚本通过检查 LeaseStatus 属性的值，查找未附加的非托管磁盘（页 blob）。 如果非托管磁盘附加到 VM，则 LeaseStatus 属性设置为“已锁定”。 如果非托管磁盘未附加，则 LeaseStatus 属性设置为“未锁定”。 脚本会检查 Azure 订阅中所有 Azure 存储帐户中的所有非托管磁盘。 当脚本找到一个 LeaseStatus 属性设置为“未锁定”的托管磁盘时，脚本将确定该磁盘为未附加。

>[!IMPORTANT]
>首先，通过将 deleteUnattachedVHDs 变量设置为 0 来运行脚本。 通过此操作可查找并查看所有未附加的非托管 VHD。
>
>在检查所有未附加磁盘后，再次运行脚本并将 deleteUnattachedVHDs 变量设置为 1。 通过此操作可删除所有未附加的非托管 VHD。
>

```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}
```

## <a name="next-steps"></a>后续步骤

[删除存储帐户](../../storage/common/storage-create-storage-account.md)




