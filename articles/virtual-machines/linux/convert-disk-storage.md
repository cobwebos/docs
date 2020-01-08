---
title: 在标准和高级 SSD 之间转换托管磁盘存储
description: 如何使用 Azure CLI 将 Azure 托管磁盘存储从标准版转换为高级版或高级版。
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431493"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>将 Azure 托管磁盘存储从标准版转换为高级版或高级版

有四种磁盘类型的 Azure 托管磁盘： Azure ultra Ssd （预览版）、高级 SSD、标准 SSD 和标准 HDD。 你可以根据性能需求在三个 GA 磁盘类型（高级 SSD、标准 SSD 和标准 HDD）之间进行切换。 你还不能从或切换到 ultra SSD，你必须部署一个新的。

非托管磁盘不支持此功能。 但可以轻松地[将非托管磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便在磁盘类型之间切换。

本文介绍如何使用 Azure CLI 将托管磁盘从标准版或高级版转换为标准版。 若要安装或升级该工具，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="before-you-begin"></a>开始之前

* 磁盘转换需要重新启动虚拟机（VM），因此请在预先存在的维护时段内计划磁盘存储的迁移。
* 对于非托管磁盘，请先[转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便可以在存储选项之间进行切换。


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>在高级和标准版之间切换 VM 的所有托管磁盘

此示例演示如何将 VM 的所有磁盘从标准存储或高级存储转换为标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还切换到了支持高级存储的大小。

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>在标准和高级之间切换各个托管磁盘

对于开发/测试工作负荷，可能需要混合使用标准磁盘和高级磁盘，以减少成本。 你可以选择仅升级那些需要更好性能的磁盘。 此示例显示了如何将单个 VM 磁盘从标准存储或高级存储转换为标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还切换到了支持高级存储的大小。

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>在标准 HDD 和标准 SSD 之间切换托管磁盘

此示例演示如何将单个 VM 磁盘从标准 HDD 转换为标准 SSD 或从标准 SSD 转换为标准 HDD。

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>在 Azure 门户中的标准和高级之间切换托管磁盘

执行以下步骤:

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 从**虚拟机**列表中选择 VM。
3. 如果未停止 VM，请选择 VM**概述**窗格顶部的 "**停止**"，并等待 VM 停止。
4. 在 VM 的窗格中，从菜单中选择 "**磁盘**"。
5. 选择要转换的磁盘。
6. 从菜单中选择 "**配置**"。
7. 将**帐户类型**从**标准 HDD**更改为**高级 SSD**或从**高级 SSD**更改为**标准 HDD**。
8. 选择 "**保存**"，然后关闭 "磁盘" 窗格。

磁盘类型的更新是即时的。 转换后，可以重启 VM。

## <a name="next-steps"></a>后续步骤

使用[快照](snapshot-copy-managed-disk.md)创建 VM 的只读副本。
