---
title: Azure 虚拟机规模集附加数据磁盘 | Microsoft 文档
description: 了解如何将附加数据磁盘与虚拟机规模集配合使用
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: ec11a2d66530129fb61d97681e6882b887c8654c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure 虚拟机规模集和附加数据磁盘
为了扩展可用存储，Azure [虚拟机规模集](/azure/virtual-machine-scale-sets/)支持包含附加数据磁盘的 VM 实例。 可以在创建规模集时附加数据磁盘，也可以将数据磁盘附加到现有规模集。

> [!NOTE]
>  创建包含附加数据磁盘的规模集时，需要在 VM 中装载并格式化这些磁盘，才能使用（就像标准 Azure VM 一样）。 若要完成此过程，可使用自定义脚本扩展来调用脚本，将 VM 中的所有数据磁盘分区和格式化，这是一种很方便的方法。 有关示例，请参阅 [Azure CLI 2.0](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks)。


## <a name="create-and-manage-disks-in-a-scale-set"></a>在规模集中创建和管理磁盘
有关如何创建包含附加数据磁盘的规模集、准备和格式化或添加和删除数据磁盘的详细信息，请参阅以下教程之一：

- [Azure CLI 2.0](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

本文的余下内容概述具体的用例，例如，需要数据磁盘的 Service Fabric 群集，或者将包含内容的现有数据磁盘附加到规模集。


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>使用附加的数据磁盘创建 Service Fabric 群集
在 Azure 中运行的 [Service Fabric](/azure/service-fabric) 群集中的每个[节点类型](../service-fabric/service-fabric-cluster-nodetypes.md)都受虚拟机规模集的支持。  可以使用 Azure 资源管理器模板将数据磁盘附加到组成 Service Fabric 群集的规模集。 可以使用[现有模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)作为起点。 在模板中包括 _Microsoft.Compute/virtualMachineScaleSets_ 资源的 _storageProfile_ 中的 _dataDisks_ 节，然后部署模板。 以下示例附加一个 128 GB 的数据磁盘：

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

可以在部署群集时自动对数据磁盘执行分区、格式化和装载操作。  向规模集的 _virtualMachineProfile_ 的 _extensionProfile_ 添加自定义脚本扩展。

若要自动准备 Windows 群集中的数据磁盘，请添加以下代码：

```json
{
    "name": "customScript",    
    "properties": {    
        "publisher": "Microsoft.Compute",    
        "type": "CustomScriptExtension",    
        "typeHandlerVersion": "1.8",    
        "autoUpgradeMinorVersion": true,    
        "settings": {    
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
若要自动准备 Linux 群集中的数据磁盘，请添加以下代码：
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>将预先填充的数据磁盘添加到现有规模集 
> 根据设计，在向现有的规模集模型添加磁盘时，创建的磁盘始终为空。 此方案还包括规模集创建的新实例。 之所以出现这样的行为，是因为规模集定义有一个空的数据磁盘。 若要为现有的规模集模型创建预先填充的数据驱动器，可以从后续的两个选项中随意选择一个：

* 通过运行自定义脚本，将数据从实例 0 VM 复制到其他 VM 中的数据磁盘。
* 使用 OS 磁盘和数据磁盘（以及必需的数据）创建一个托管映像，然后使用该映像创建新的规模集。 这样一来，每个新创建的 VM 都有一个数据磁盘，该磁盘是在规模集的定义中规定的。 由于该定义引用带数据磁盘的映像，且该磁盘包含自定义数据，因此规模集中每个虚拟机都带有这些更改。

> 若要创建自定义映像，可参阅以下方法：[在 Azure 中创建通用 VM 的托管映像](/azure/virtual-machines/windows/capture-image-resource/) 

> 用户需捕获包含必需数据的实例 0 VM，然后将该 vhd 用于映像定义。


## <a name="additional-notes"></a>附加说明
Microsoft.Compute API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) 或更高版本中提供了对 Azure 托管磁盘和规模集附加数据磁盘的支持。

在一开始实施对规模集的附加磁盘支持时，不能对规模集中的各个 VM 执行数据磁盘的附加或分离操作。

Azure 门户对规模集中附加数据磁盘的支持一开始是受限的。 可以根据需要使用 Azure 模板、CLI、PowerShell、SDK 和 REST API 管理附加磁盘。


