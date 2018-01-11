---
title: "Azure 虚拟机规模集附加数据磁盘 | Microsoft 文档"
description: "了解如何将附加数据磁盘与虚拟机规模集配合使用"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: 355865b963c313097f7f5900007f341dba92bf67
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2017
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure 虚拟机规模集和附加数据磁盘
Azure [虚拟机规模集](/azure/virtual-machine-scale-sets/)现在支持对虚拟机附加数据磁盘。 对于使用 Azure 托管磁盘创建的规模集，可以在存储配置文件中定义数据磁盘。 以前，适用于规模集中 VM 的唯一直接附加存储选项是 OS 驱动器和临时驱动器。

> [!NOTE]
>  即使在创建规模集时定义了附加数据磁盘，也仍需在 VM 中装载并格式化这些磁盘，才能使用（就像标准 Azure VM 一样）。 若要完成此过程，可使用自定义脚本扩展来调用标准脚本，将 VM 中的所有数据磁盘分区和格式化，这是一种很方便的方法。

## <a name="create-a-scale-set-with-attached-data-disks"></a>创建包含附加数据磁盘的规模集
若要创建包含附加磁盘的规模集，一种简单的方式是使用 [az vmss create](/cli/azure/vmss#create) 命令。 以下示例创建一个 Azure 资源组，以及一个包含 10 个 Ubuntu VM 的虚拟机规模集，每个 VM 有 2 个附加数据磁盘，分别为 50 GB 和 100 GB。

```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

[az vmss create](/cli/azure/vmss#create) 命令会对某些配置值进行默认设置（如果用户未指定这些值）。 若要查看可重写的选项，请尝试以下命令：

```bash
az vmss create --help
```

要创建包含附加数据磁盘的规模集，另一种方法是在 Azure 资源管理器模板中定义一个规模集，在 _storageProfile_ 中包括 _dataDisks_ 节，并部署模板。 将定义前一示例中的 50 GB 和 100 GB 磁盘，如以下模板示例所示：

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```

对于定义了附加磁盘的规模集模板，如需可供部署的完整示例，请访问 [https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data)。

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>将数据磁盘添加到现有规模集
> [!NOTE]
>  只能将数据磁盘附加到使用 [Azure 托管磁盘](./virtual-machine-scale-sets-managed-disks.md)创建的规模集。

可以使用 Azure CLI _az vmss disk attach_ 命令将数据磁盘添加到虚拟机规模集。 请确保指定尚未使用的 LUN。 以下 CLI 示例将 50 GB 的驱动器添加到 LUN 3：

```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

以下 PowerShell 示例将 50 GB 的驱动器添加到 LUN 3：

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> 不同的 VM 大小对所支持的附加驱动器数目有不同的限制。 在添加新磁盘之前，请检查[虚拟机大小特征](../virtual-machines/windows/sizes.md)。

也可通过以下方式添加磁盘：先向规模集定义的 _storageProfile_ 中的 _dataDisks_ 属性添加新条目，然后应用所做的更改。 若要测试这一点，请在 [Azure 资源浏览器](https://resources.azure.com/)中找到一项现有的规模集定义。 选择“编辑”，然后将新磁盘添加到数据磁盘列表，如以下示例所示：

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

然后选择“PUT”，应用对规模集的更改。 只要使用的 VM 大小支持两个以上的附加数据磁盘，此示例就可以运行。

> [!NOTE]
> 对规模集定义进行更改时（例如添加或删除数据磁盘），所做的更改将应用于所有新创建的 VM，但只在 _upgradePolicy_ 属性设置为“Automatic”的情况下，才会应用到现有的 VM。 如果设置为“Manual”，则需手动将新模型应用到现有的 VM。 可以在门户中执行此操作，使用 _Update-AzureRmVmssInstance_ PowerShell 命令或 _az vmss update-instances_ CLI 命令。

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>将预先填充的数据磁盘添加到现有规模集 
> 根据设计，在向现有的规模集模型添加磁盘时，创建的磁盘始终为空。 此方案还包括规模集创建的新实例。 之所以出现这样的行为，是因为规模集定义有一个空的数据磁盘。 若要为现有的规模集模型创建预先填充的数据驱动器，可以从后续的两个选项中随意选择一个：

* 通过运行自定义脚本，将数据从实例 0 VM 复制到其他 VM 中的数据磁盘。
* 使用 OS 磁盘和数据磁盘（以及必需的数据）创建一个托管映像，然后使用该映像创建新的规模集。 这样一来，每个新创建的 VM 都有一个数据磁盘，该磁盘是在规模集的定义中规定的。 由于该定义引用带数据磁盘的映像，且该磁盘包含自定义数据，因此规模集中每个虚拟机都带有这些更改。

> 若要创建自定义映像，可参阅以下方法：[在 Azure 中创建通用 VM 的托管映像](/azure/virtual-machines/windows/capture-image-resource/) 

> 用户需捕获包含必需数据的实例 0 VM，然后将该 vhd 用于映像定义。

## <a name="removing-a-data-disk-from-a-scale-set"></a>从规模集中移除数据磁盘
可以使用 Azure CLI _az vmss disk detach_ 命令从虚拟机规模集中移除数据磁盘。 例如，以下命令移除在 LUN 2 上定义的磁盘：
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
类似地，也可通过以下方式从规模集中移除磁盘：删除 _storageProfile_ 的 _dataDisks_ 属性中的条目，并应用所做的更改。 

## <a name="additional-notes"></a>附加说明
Microsoft.Compute API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) 或更高版本中提供了对 Azure 托管磁盘和规模集附加数据磁盘的支持。

在一开始实施对规模集的附加磁盘支持时，不能对规模集中的各个 VM 执行数据磁盘的附加或分离操作。

Azure 门户对规模集中附加数据磁盘的支持一开始是受限的。 可以根据需要使用 Azure 模板、CLI、PowerShell、SDK 和 REST API 管理附加磁盘。


