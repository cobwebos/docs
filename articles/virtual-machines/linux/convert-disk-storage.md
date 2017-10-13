---
title: "Azure 标准与高级托管磁盘存储的相互转换 | Microsoft Docs"
description: "如何使用 Azure CLI 实现 Azure 标准与高级托管磁盘存储的相互转换。"
services: virtual-machines-linux
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 0380b4aaa23b4aaba4c67d05e2d62f3ef41d6a32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Azure 标准与高级托管磁盘存储的相互转换

托管磁盘提供两种存储选项：[高级](../../storage/storage-premium-storage.md)（基于 SSD）和[标准](../../storage/storage-standard-storage.md)（基于 HDD）。 它可让你基于性能需求在这两个选项之间轻松切换，并保障最短停机时间。 非托管磁盘不具备此功能。 但可以轻松[转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便在这两个选项之间轻松切换。

本文介绍了如何使用 Azure CLI 实现 Azure 标准与高级托管磁盘存储的相互转换。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli.md)。 

## <a name="before-you-begin"></a>开始之前

* 该转换需要重启 VM，因此请在预先存在的维护时段内计划磁盘存储迁移。 
* 如果你使用非托管磁盘，请首先[转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便按照本文中的说明在两个存储选项之间切换。 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>实现 VM 所有标准与高级托管磁盘的相互转换

下方示例展示如何将 VM 的所有磁盘从标准存储切换到高级存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还切换到了支持高级存储的大小。

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>标准与高级托管磁盘的相互转换

对于开发/测试工作负荷，你可能想要同时具有标准磁盘和高级磁盘，以减少成本。 可通过仅将需要更佳性能的磁盘升级到高级存储来实现此目的。 下方示例展示如何将 VM 的单个磁盘在标准存储与高级存储之间相互切换。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还切换到了支持高级存储的大小。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>后续步骤

使用[快照](snapshot-copy-managed-disk.md)获取 VM 的只读副本。

