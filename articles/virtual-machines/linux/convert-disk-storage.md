---
title: 转换 Azure 托管磁盘存储的标准到高级或标准到高级版 |Microsoft Docs
description: 如何将 Azure 托管磁盘存储的标准到高级或标准到高级版使用 Azure CLI。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 18730f662f36000e1efc826c35bebde79dbf0e79
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013613"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>转换 Azure 托管磁盘存储的标准到高级或标准到高级版

有四个[磁盘类型](disks-types.md)适用于 Azure 托管磁盘：Azure 超高磁盘存储、 高级 SSD、 标准 SSD 和标准 HDD。 可以轻松地切换高级 SSD、 标准 SSD 和标准 HDD 根据性能需求与短停机时间。 非托管的磁盘或超高的磁盘存储不支持此功能。 但您可以轻松地[转换为托管磁盘的非托管](convert-unmanaged-to-managed-disks.md)能够磁盘类型之间切换。

本文介绍如何将托管的磁盘从标准转换为高级或标准到高级版，使用 Azure CLI。 若要安装或升级该工具，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="before-you-begin"></a>开始之前

* 磁盘转换需要重新启动虚拟机 (VM)，因此请计划在预先存在的维护时段的磁盘存储迁移。
* 对于非托管磁盘，第一个[转换为托管磁盘](convert-unmanaged-to-managed-disks.md)以便可以存储选项之间切换。


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>切换高级和标准版之间将 VM 的所有托管的磁盘

此示例演示如何将转换的所有虚拟机的磁盘从标准到高级存储或从 premium 升级到标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还切换到了支持高级存储的大小。

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>切换标准和高级版之间的各个托管的磁盘

针对开发/测试工作负荷，你可能想要混合使用标准和高级磁盘，来降低成本。 您可以选择升级仅需要更好的性能这些磁盘。 此示例演示如何将单个 VM 磁盘从标准到高级存储或从 premium 升级到标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还切换到了支持高级存储的大小。

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>切换标准 HDD 和 SSD 标准之间的托管的磁盘

此示例演示如何将单个 VM 磁盘从标准 hdd 升级到标准的 SSD 或标准 SSD 到标准 HDD。

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>切换标准和高级版之间在 Azure 门户中的托管的磁盘

执行以下步骤:

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 从列表中选择的 VM**虚拟机**。
3. 如果 VM 未停止，则选择**停止**VM 顶部**概述**窗格中，并等待要停止的 VM。
4. 在 vm 窗格中选择**磁盘**菜单中。
5. 选择你想要转换的磁盘。
6. 选择**配置**菜单中。
7. 更改**帐户类型**从**标准 HDD**到**高级 SSD**或从**高级 SSD**到**标准 HDD**.
8. 选择**保存**，并关闭磁盘窗格。

磁盘类型的更新是瞬间完成的。 在转换后，可以重新启动 VM。

## <a name="next-steps"></a>后续步骤

使用创建 VM 的只读副本[快照](snapshot-copy-managed-disk.md)。
