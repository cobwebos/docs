---
title: "查找并删除未连接的 Azure 托管和非托管磁盘 | Microsoft Docs"
description: "如何使用 Azure PowerShell 查找并删除未连接的 Azure 托管和非托管（VHD/页 blob）磁盘"
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
ms.openlocfilehash: a846d3578d40b19762f185381c92bdf8e225b185
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>查找并删除未连接的 Azure 托管和非托管磁盘
删除 Azure 中的虚拟机时，默认为不删除连接到它的磁盘。 这样可以防止因误删虚拟机而丢失数据，但需要继续为未连接的磁盘支付不必要的费用。 通过本文可查找并删除所有未连接的磁盘，以便节省成本。 


## <a name="find-and-delete-unattached-managed-disks"></a>查找并删除未连接的托管磁盘 

以下脚本展示如何使用 *ManagedBy* 属性查找未连接的[托管磁盘](managed-disks-overview.md)。 它循环访问订阅中的所有托管磁盘，并检查 *ManagedBy* 属性是否为 NULL，以查找未连接的托管磁盘。 *ManagedBy* 属性存储了托管磁盘连接到的虚拟机的资源 ID。

强烈建议先通过将 *deleteUnattachedDisks* 变量设置为 0 来运行脚本，以查看所有未连接的磁盘。 查看了未连接的磁盘后，通过将 *deleteUnattachedDisks* 设置为 1 来运行脚本，以删除所有未连接的磁盘。

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
## <a name="find-and-delete-unattached-unmanaged-disks"></a>查找并删除未连接的非托管磁盘 

非托管磁盘是指以[页 blob] (/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) 形式存储在 [Azure 存储帐户](../../storage/common/storage-create-storage-account.md)中的 VHD 文件。 以下脚本展示如何使用 *LeaseStatus* 属性查找未连接的非托管磁盘（页 blob）。 它循环访问订阅中所有存储帐户中的所有非托管磁盘，并检查 *LeaseStatus* 属性是否为“已解锁”，以查找未连接的非托管磁盘。 如果非托管磁盘连接到虚拟机，则 *LeaseStatus* 属性设置为“已锁定”。

强烈建议先通过将 *deleteUnattachedVHDs* 变量设置为 0 来运行脚本，以查看所有未连接的磁盘。 查看了未连接的磁盘后，通过将 *deleteUnattachedVHDs* 设置为 1 来运行脚本，以删除所有未连接的磁盘。


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




