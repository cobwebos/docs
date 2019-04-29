---
title: 转换 Azure 托管磁盘存储的标准到高级或标准到高级版 |Microsoft Docs
description: 如何使用 Azure CLI 将 Azure 托管磁盘存储从标准类型转换为高级类型，或者从高级类型转换为标准类型。
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2e7eb455a53abbe2df6ff72f091a599665732429
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765741"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>将 Azure 托管磁盘存储从标准类型转换为高级类型，或者从高级类型转换为标准类型

有四个[磁盘类型](disks-types.md)适用于 Azure 托管磁盘：Azure 超高磁盘存储、 高级 SSD、 标准 SSD 和标准 HDD。 可以根据性能需求，轻松在高级 SSD、标准 SSD 和标准 HDD 之间切换，此过程只会造成短暂的停机。 非托管的磁盘或超高的磁盘存储不支持此功能。 但您可以轻松地[转换为托管磁盘的非托管](convert-unmanaged-to-managed-disks.md)能够磁盘类型之间切换。

本文介绍如何使用 Azure CLI 将托管磁盘从标准类型转换为高级类型，或者从高级类型转换为标准类型。 若要安装或升级 CLI 工具，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="before-you-begin"></a>开始之前

* 磁盘转换需要重启虚拟机 (VM)，因此请在预先存在的维护时段内计划磁盘存储迁移。
* 对于非托管磁盘，请先[转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便可以在存储选项之间切换。


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>将 VM 的所有托管磁盘在高级类型与标准类型之间切换

此示例演示如何将 VM 的所有磁盘从标准存储转换为高级存储，或者从高级存储转换为标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还会切换到支持高级存储的大小。

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>在标准类型与高级类型之间切换单个托管磁盘

对于开发/测试工作负荷，可以混合使用标准磁盘和高级磁盘来降低成本。 可以选择仅升级需要更高性能的磁盘。 此示例演示如何将单个 VM 磁盘从标准存储转换为高级存储，或者从高级存储转换为标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还会切换到支持高级存储的大小。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>在标准 HDD 与标准 SSD 之间切换托管磁盘

此示例演示如何将单个 VM 磁盘从标准 HDD 转换为标准 SSD，或者从标准 SSD 转换为标准 HDD。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>使用 Azure 门户在标准类型与高级类型之间切换托管磁盘

执行以下步骤:

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“虚拟机”列表中选择 VM。
3. 如果该 VM 未停止，请在 VM“概述”窗格的顶部选择“停止”，然后等待该 VM 停止。
4. 在 VM 对应的窗格中，从菜单中选择“磁盘”。
5. 选择要转换的磁盘。
6. 在菜单中选择“配置”。
7. 将“帐户类型”从“标准 HDD”更改为“高级 SSD”，或者从“高级 SSD”更改为“标准 HDD”。
8. 选择“保存”并关闭磁盘窗格。

磁盘类型会即时更新。 转换后，可以重启 VM。

## <a name="next-steps"></a>后续步骤

使用[快照](snapshot-copy-managed-disk.md)创建 VM 的只读副本。
