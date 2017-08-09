---
title: "在 Azure 资源管理器模板中使用托管磁盘 | Microsoft Docs"
description: "详细介绍如何在 Azure 资源管理器模板中使用托管磁盘"
services: storage
documentationcenter: 
author: jboeshart
manager: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/01/2017
ms.author: jaboes
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 4c502784a57850d6f11200e95f7432d2206e920a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/28/2017

---

# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>在 Azure 资源管理器模板中使用托管磁盘

本文档逐步讲解在使用 Azure 资源管理器模板预配虚拟机时托管磁盘与非托管磁盘之间的差异。 这可以帮助你将使用非托管磁盘的现有模板更新为托管磁盘。 为方便参考，我们将使用 [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) 模板作为指导。 你可以查看使用[托管磁盘](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json)的模板，以及使用[非托管磁盘](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json)的早期版本，因此可以根据需要直接对两者进行比较。

## <a name="unmanaged-disks-template-formatting"></a>非托管磁盘模板的格式

在开始之前，我们先了解一下非托管磁盘的部署方式。 创建非托管磁盘时，需要一个用于保存 VHD 文件的存储帐户。 可以创建新存储帐户，或使用现有的存储帐户。 本文说明如何创建新存储帐户。 若要实现此目的，资源块中需有一个如下所示的存储帐户资源。

```
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2016-01-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

在虚拟机对象中，需要在存储帐户中创建一个依赖项才能确保先创建存储帐户，再创建虚拟机。 然后，在 `storageProfile` 节中，我们将指定 VHD 位置的完整 URI，该 URI 引用存储帐户，并且 OS 磁盘和任何数据磁盘都需要它。 

```
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
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

若要使用托管磁盘创建 VM，你不再需要创建存储帐户资源，而可以按如下所示更新虚拟机资源。 具体请注意，`apiVersion` 反映 `2017-03-30`，`osDisk` 和 `dataDisks` 不再引用 VHD 的特定 URI。 如果部署时未指定其他属性，磁盘将使用[标准 LRS 存储](storage-redundancy.md)。 如果未指定名称，OS 磁盘的名称格式将采用 `<VMName>_OsDisk_1_<randomstring>`，每个数据磁盘的名称格式将采用 `<VMName>_disk<#>_<randomstring>`。 Azure 磁盘加密默认已禁用；OS 磁盘的缓存设置为“读/写”，数据磁盘的缓存设置为“无”。 在以下示例中，你可能会注意到还有一个存储帐户依赖项，不过，此依赖项只用于诊断信息的存储，而磁盘存储并不需要它。

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
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

```
{
    "type": "Microsoft.Compute/disks",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "apiVersion": "2017-03-30",
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

然后，在 VM 对象中，可以引用这个要附加的磁盘对象。 指定在 `managedDisk` 属性中创建的托管磁盘的资源 ID 可以在创建 VM 时附加该磁盘。 请注意，VM 资源的 `apiVersion` 设置为 `2017-03-30`。 另请注意，我们已在磁盘资源中创建了一个依赖项，以确保在创建 VM 之前成功创建该磁盘资源。 

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
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

若要使用托管磁盘创建包含 VM 的托管可用性集，请将 `sku` 对象添加到可用性集资源，并将 `name` 属性设置为 `Aligned`。 这可确保每个 VM 的磁盘彼此充分隔离，避免发生单点故障。 另请注意，可用性集资源的 `apiVersion` 设置为 `2017-03-30`。

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/availabilitySets",
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

### <a name="additional-scenarios-and-customizations"></a>其他方案和自定义

若要查找有关 REST API 规范的完整信息，请查看[有关创建托管磁盘 REST API 的文档](/rest/api/manageddisks/disks/disks-create-or-update)， 其中介绍了其他方案，以及可以通过模板部署提交到 API 的默认值和可接受值。 

## <a name="next-steps"></a>后续步骤

* 有关使用托管磁盘的完整模板，请访问以下 Azure 快速入门存储库链接。
    * [使用托管磁盘的 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [使用托管磁盘的 Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [托管磁盘模板的完整列表](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* 请访问 [Azure 托管磁盘概述](storage-managed-disks-overview.md)文档，了解有关托管磁盘的详细信息。
* 请访问 [Microsoft.Compute/virtualMachines 模板参考](/templates/microsoft.compute/virtualmachines)文档，查看虚拟机资源的模板参考文档。
* 请访问 [Microsoft.Compute/disks 模板参考](/templates/microsoft.compute/disks)文档，查看磁盘资源的模板参考文档。
 

