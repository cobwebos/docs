---
title: "Azure 资源属性的多个副本 | Microsoft Docs"
description: "在 Azure Resource Manager 模板中，在资源类型上创建属性的多个实例。 必须使用 take 函数，而不是此方案的 copy 循环。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: da79ff58e892bde5a35456992105de9bdbe36364
ms.lasthandoff: 04/19/2017


---
# <a name="create-multiple-instances-of-property-on-resource-type"></a>在资源类型上创建属性的多个实例
只能对资源类型，而不能对资源类型中的属性使用 [copy](resource-group-create-multiple.md)。 若要为资源中的某部分创建多个实例，此要求可能会造成一些问题。 一种常见的情况是为一个虚拟机创建多个数据磁盘。 无法对数据磁盘使用 copy，因为 dataDisks 是虚拟机的一个属性，而不是它的资源类型。 可以根据需要使用足够多的数据磁盘创建数组，并传递要创建的数据磁盘的实际数量。 在虚拟机定义中，使用 `take` 函数从数组中仅获取实际需要的元素数。

[使用动态选择的数据磁盘创建 VM](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) 模板中显示了此模式的完整示例。

部署模板的相关节如下文所示。 已删除模板中的部分节，以便突出显示动态创建多个数据磁盘时所涉及的节。 

## <a name="define-template-with-variable-for-the-property"></a>使用属性的变量定义模板

若要创建具有多个数据磁盘的虚拟机，请添加一个数组变量，该变量定义的数据磁盘的数量超过所需数量。 在参数 `numDataDisks` 中，传递要创建的实际数据磁盘数量。 使用 `take` 指定数组中要分配给虚拟机的精确元素数量。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ... /* not all elements shown */
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        "storageProfile": {
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

## <a name="create-multiple-virtual-machines-with-multiple-data-disks"></a>创建具有多个数据磁盘的多个虚拟机

当需要使用属性的可变数量项目创建资源的多个实例时，可以使用 **take** 函数和 **copy** 元素。 例如，假设需要创建多个虚拟机，但每个虚拟机具有不同数量的数据磁盘。 

若要给每个数据磁盘一个用于标识关联的虚拟机的名称，请将数据磁盘数组放入单独的模板中。 包括虚拟机名称参数和要返回的数据磁盘数。 在输出部分中，返回指定项的数目。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

在父模板中，包括虚拟机数量的参数，以及每个虚拟机的数据磁盘数量的数组。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

在资源部分中，部署定义数据磁盘的模板的多个实例。 

```json
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

在资源部分中，部署虚拟机的多个实例。 对于数据磁盘，请引用包含正确数量的数据磁盘和数据磁盘的正确名称的嵌套部署。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="next-steps"></a>后续步骤
* 若要了解如何创建一个资源的多个实例，请参阅[在 Azure Resource Manager 模板中部署多个资源实例](resource-group-create-multiple.md)。
* 若要按顺序创建循环访问的资源，请参阅 [Azure Resource Manager 模板的顺序循环](resource-manager-sequential-loop.md)。
* 若要了解如何部署模板，请参阅[使用 Azure Resource Manager 模板部署应用程序](resource-group-template-deploy.md)。


