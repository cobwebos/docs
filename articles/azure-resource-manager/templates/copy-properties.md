---
title: 定义属性的多个实例
description: 在 Azure 资源管理器模板中使用复制操作在资源上创建属性时多次迭代。
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: e86d38b0e5d2e39d54b3c419b6eebdcda74022db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258101"
---
# <a name="property-iteration-in-arm-templates"></a>ARM 模板中的属性迭代

本文介绍如何在 Azure 资源管理器 （ARM） 模板中创建多个属性实例。 通过将**复制**元素添加到模板中资源的属性部分，可以在部署期间动态设置属性的项数。 您还避免重复模板语法。

您还可以使用复制与[资源](copy-resources.md)、[变量](copy-variables.md)和[输出](copy-outputs.md)。

## <a name="property-iteration"></a>属性迭代

复制元素具有以下常规格式：

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

对于**名称**，请提供要创建的资源属性的名称。 **count**属性指定所需的属性的迭代数。

**输入**属性指定要重复的属性。 创建从**输入**属性中的值构造的元素数组。

以下示例演示了如何将 `copy` 应用于虚拟机上的 dataDisks 属性：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

请注意，在属性迭代中使用 `copyIndex` 时，必须提供迭代的名称。

> [!NOTE]
> 属性迭代还支持偏移参数。 偏移量必须以迭代的名称（如 copyIndex（"数据磁盘"1）之后提供。
>

Resource Manager 在部署期间扩展 `copy` 数组。 数组的名称将成为属性的名称。 输入值将成为对象属性。 已部署的模板将成为：

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

copy 元素是一个数组，因此，可以为资源指定多个属性。

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

可将资源和属性迭代一起使用。 按名称引用属性迭代。

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>复制限制

count 不能超过 800。

count 不能为负数。 如果使用 Azure PowerShell 2.6 或更高版本、Azure CLI 2.0.74 或更高版本或者 REST API 版本 **2019-05-10** 或更高版本部署模板，则可以将 count 设置为零。 更早版本的 PowerShell、CLI 和 REST API 不支持将 count 设为零。

## <a name="example-templates"></a>示例模板

下面的示例显示了为属性创建多个值的常见方案。

|模板  |描述  |
|---------|---------|
|[部署数据磁盘数量不定的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |通过虚拟机部署多个数据磁盘。 |

## <a name="next-steps"></a>后续步骤

* 要浏览教程，请参阅[教程：使用 ARM 模板创建多个资源实例](template-tutorial-create-multiple-instances.md)。
* 有关复制元素的其他用途，请参阅：
  * [ARM 模板中的资源迭代](copy-resources.md)
  * [ARM 模板中的可变迭代](copy-variables.md)
  * [ARM 模板中的输出迭代](copy-outputs.md)
* 如果要了解模板的各个部分，请参阅[创作 ARM 模板](template-syntax.md)。
* 要了解如何部署模板，请参阅[使用 ARM 模板部署应用程序](deploy-powershell.md)。

