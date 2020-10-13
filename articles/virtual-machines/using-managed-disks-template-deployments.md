---
title: 用 Azure 资源管理器模板部署磁盘
description: 详细介绍如何在 azure Vm 中使用 azure 资源管理器模板中的托管和非托管磁盘。
documentationcenter: ''
author: jboeshart
manager: ''
ms.service: virtual-machines
ms.topic: how-to
ms.workload: storage
ms.date: 06/01/2017
ms.author: jaboes
ms.subservice: disks
ms.openlocfilehash: 7c66a8b8483673a9d8fbdc9922b9cc377781bab3
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976653"
---
# <a name="using-disks-in-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板中的磁盘

本文档逐步讲解在使用 Azure 资源管理器模板预配虚拟机时托管磁盘与非托管磁盘之间的差异。 该示例可以帮助你将使用非托管磁盘的现有模板更新为托管磁盘。 为方便参考，我们将使用 [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) 模板作为指导。 你可以查看使用[托管磁盘](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json)的模板，以及使用[非托管磁盘](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json)的早期版本，因此可以根据需要直接对两者进行比较。

## <a name="unmanaged-disks-template-formatting"></a>非托管磁盘模板的格式

在开始之前，我们先了解一下非托管磁盘的部署方式。 创建非托管磁盘时，需要一个用于保存 VHD 文件的存储帐户。 可以创建新存储帐户，或使用现有的存储帐户。 本文说明如何创建新存储帐户。 在资源块中创建一个如下所示的存储帐户资源。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

在虚拟机对象中，在存储帐户中添加一个依赖项才能确保先创建存储帐户，再创建虚拟机。 然后，在 `storageProfile` 节中，指定 VHD 位置的完整 URI，该 URI 引用存储帐户，并且 OS 磁盘和任何数据磁盘都需要它。

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>托管磁盘模板的格式

具有 Azure 托管磁盘时，该磁盘将成为顶级资源，用户不再需要创建存储帐户。 托管磁盘在 `2016-04-30-preview` API 版本中首次公开，并在所有后续 API 版本中可用，现在是默认磁盘类型。 以下部分逐步讲解默认设置，并详细说明如何进一步自定义磁盘。

> [!NOTE]
> 建议使用 `2016-04-30-preview` 以后的 API 版本，因为在 `2016-04-30-preview` 和 `2017-03-30` 之间存在重大更改。
>
>

### <a name="default-managed-disk-settings"></a>默认的托管磁盘设置

若要使用托管磁盘创建 VM，则不再需要创建存储帐户资源。 请参考下面的模板示例，需要注意，这与前面的非托管磁盘示例有些不同：

- `apiVersion` 是一个支持托管磁盘的版本。
- `osDisk` 和 `dataDisks` 不再引用 VHD 的特定 URI。
- 如果部署时未指定其他属性，磁盘将根据 VM 大小使用某个存储类型。 例如，如果你使用的是支持高级存储的 VM 大小（其名称中包含“s”的大小，例如 Standard_D2s_v3），则默认情况下将配置高级磁盘。 若要更改此行为，可以使用磁盘的 SKU 设置来指定存储类型。
- 如果没有为磁盘指定名称，则 OS 磁盘采用格式 `<VMName>_OsDisk_1_<randomstring>`，每个数据磁盘采用格式 `<VMName>_disk<#>_<randomstring>`。
  - 如果正在基于自定义映像创建 VM，则会从在自定义映像资源中定义的磁盘属性中检索存储帐户类型和磁盘名称的默认设置。 可以通过在模板中指定这些值来替代它们。
- 默认情况下，Azure 磁盘加密处于禁用状态。
- 默认情况下，缓存对于 OS 磁盘为“读/写”，对于数据磁盘则为“无”。
- 下面的示例中仍然存在一个存储帐户依赖项，但这仅用于诊断的存储，磁盘存储并不需要。

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>使用顶级托管磁盘资源

在虚拟机对象中指定磁盘配置的一种替代方法是创建一个顶级磁盘资源，并在创建虚拟机的过程中附加该资源。 例如，可按如下所示创建一个用作数据磁盘的磁盘资源。

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

在 VM 对象中，引用要附加的磁盘对象。 指定在 `managedDisk` 属性中创建的托管磁盘的资源 ID 可以在创建 VM 时附加该磁盘。 该 VM 资源的 `apiVersion` 设置为 `2017-03-30`。 在磁盘资源中添加了一个依赖项，以确保在创建 VM 之前成功创建该磁盘资源。 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>使用托管磁盘创建包含 VM 的托管可用性集

若要使用托管磁盘创建包含 VM 的托管可用性集，请将 `sku` 对象添加到可用性集资源，并将 `name` 属性设置为 `Aligned`。 该属性可确保每个 VM 的磁盘彼此充分隔离，避免发生单点故障。 另请注意，可用性集资源的 `apiVersion` 设置为 `2018-10-01`。

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>标准 SSD 盘

以下为创建标准 SSD 盘时资源管理器模板中所需的参数：

* Microsoft.Compute 的 apiVersion 必须设置为 `2018-04-01`（或更高）
* 将 managedDisk.storageAccountType 指定为 `StandardSSD_LRS`

以下示例显示了使用标准 SSD 盘的 VM 的 properties.storageProfile.osDisk 部分：

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

有关如何使用模板创建标准 SSD 盘的完整模板示例，请参阅[使用标准 SSD 数据磁盘从 Windows 映像创建 VM](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)。

### <a name="additional-scenarios-and-customizations"></a>其他方案和自定义

若要查找有关 REST API 规范的完整信息，请查看[有关创建托管磁盘 REST API 的文档](/rest/api/manageddisks/disks/disks-create-or-update)， 其中介绍了其他方案，以及可以通过模板部署提交到 API 的默认值和可接受值。 

## <a name="next-steps"></a>后续步骤

* 有关使用托管磁盘的完整模板，请访问以下 Azure 快速入门存储库链接。
    * [使用托管磁盘的 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [使用托管磁盘的 Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* 请访问 [Azure 托管磁盘概述](managed-disks-overview.md)文档，了解有关托管磁盘的详细信息。
* 请访问 [Microsoft.Compute/virtualMachines 模板参考](/azure/templates/microsoft.compute/virtualmachines)文档，查看虚拟机资源的模板参考文档。
* 请访问 [Microsoft.Compute/disks 模板参考](/azure/templates/microsoft.compute/disks)文档，查看磁盘资源的模板参考文档。
* 有关如何使用 Azure 虚拟机规模集中的托管磁盘的信息，请访问[将数据磁盘与规模集配合使用](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md)文档。